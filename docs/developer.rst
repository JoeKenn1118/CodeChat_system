***********************
Developer documentation
***********************
The CodeChat System integrates the capabilities of the `CodeChat renderer <https://codechat.readthedocs.io/>`_ into popular text editors [#]_. To support multiple editors, this program offloads most of the processing to the CodeChat Server and display to the CodeChat Client, making the editor plugin/extension code shorter and easier to port. The basic architecture:

.. digraph:: architecture

    bgcolor = transparent;
    compound = true;
    node [shape = box];
    subgraph cluster_text_editor {
        label = <Text editor/IDE>;
        source_code [label = <Source<br />code>, style = dashed];
        CodeChat_plugin [label = <CodeChat<br />plugin>];
    }

    subgraph cluster_server {
        label = <CodeChat Server>;
        thrift_server [label = <Thrift<br />server>];
        websocket_server [label = <Websocket<br />server>];
        web_server [label = <Web <br />server>];
        renderers [label = <Built-in<br />renderers>];
    }

    external_renderers [label = <External <br />renderers>];

    subgraph cluster_client {
        label = "CodeChat Client";
        rendered_code [label = <Rendered code>, style = dashed];
        JavaScript;
    }

    CodeChat_plugin -> thrift_server [label = <Thrift>, dir = both];
    websocket_server -> JavaScript [label = <websocket>, dir = both];
    web_server -> JavaScript [label = <HTTP>, dir = both, lhead = cluster_client];
    renderers -> external_renderers [label = <subprocess>, ltail = cluster_server, dir = both];

This approach bridges the services CodeChat provides, which are defined in Python, to the variety of programming languages which various text editors require. To accomplish these goals, this project:

#.  Develops a `CodeChat Server <../CodeChat_Server/contents>` to provide the needed services;
#.  Provides a `CodeChat Client <../CodeChat_Server/CodeChat_Server/CodeChat_Client/contents>`, hosted in a web browser, to display the rendered source code and provide for user input;
#.  Introduces an `extensions for various text editors/IDEs <../extensions/contents>`; and
#.  Employs `Apache Thrift <https://thrift.apache.org>`_ to define `CodeChat Services <../CodeChat_Services/contents>`, which allows the CodeChat Server to communicate with extension/plugins developed in a `variety of languages <https://thrift.apache.org/docs/Languages>`_.


Contents
========
.. toctree::
    :maxdepth: 2

    ../CodeChat_Server/contents
    ../CodeChat_Server/CodeChat_Server/CodeChat_Client/contents
    ../extensions/contents
    ../CodeChat_Services/contents
    CHANGELOG
    ../conf.py
    ../run_sphinx.bat
    ../.gitignore


Release procedures
==================
A release of the CodeChat System involves releasing the CodeChat Server and all extensions/plugins. See the follow developer docs for instructions:

-   `CodeChat Server <../CodeChat_Server/developer>`
-   `Extensions <../extensions/developer>`


To do
=====
-   Implement `settings system`.
-   Better packaging: create a bootstrap Python script to install and configure the CodeChat server (create a venv, etc.). In progress -- see the ``installer`` branch.
-   Make it easy to create a new project by offering a ``CodeChat_Server create <template name> <optional dest dir>`` command.
-   Lots of testing.
-   Inform the editor plugin when the client shuts down.
-   Handle syntax error line number click from the web client. In progress -- see the ``hyperlink-errors`` branch.
-   Hyperlink the file and line, or perhaps just the line, instead of the entire error message. In progress -- see the ``hyperlink-errors`` branch.
-   Provide way to do a rebuild all.
-   Add a CodeChat Client GUI to select render language for non-project builds.
-   Handle VS Code's themes (dark, high-contrast, etc.) correctly. To do this, we need to inherit CSS from the webview into the iframe. See https://code.visualstudio.com/api/extension-guides/webview#theming-webview-content, and also use the Developer Tools is VS Code to inspect the iframe containing the webview, which provides vars defining all the VS Code styles. In process. Instead of tying this to VS themes, CodeChat will have its own set of themes. See the ``dark-mode`` branch.
-   Add in sync. In progress -- see the ``sync`` branch.
-   Monitor iframe location changes and try to sync by loading another file.
-   Allow user-defined JSON of mapping from extensions to renderers.
-   Provide a nicer style for docutils renders.
-   Support all the pandoc renderers.
-   Improve docs.
-   Support at least one more editor. In progress -- see the ``Intellij`` and ``notepad++_extension`` branches.
-   Save and restore scroll position on a per-file basis.
-   Define a StrictYAML config file to replace the ``GLOB_TO_CONVERTER`` data structure more flexibly. Add in a bunch of conversions using Pandoc.
-   Would it be easier for extension authors if the server could be invoked from the command line in client mode to communicate with the server via stdio? For example, send render requests as JSON and receive replies as JSON, or something like that? For now, wait until more extensions are developed.
-   The mdbook render seems slow. Perhaps cache rendered results instead of recomputing them each time?
-   Provide a way to close CodeChat when running in browser mode.
-   Dynamically assign all ports.

    #.  The http port is easiest to change. See `this SO <https://stackoverflow.com/questions/1365265/on-localhost-how-do-i-pick-a-free-port-number>`_.
    #.  Next, make the websocket port dynamically assigned. The render manager would set it, then the server would need to ask the render manager for it.
    #.  Finally, make the Thrift port dynamically assigned. Perhaps return the port number in the exit code of the ``CodeChat_Server start`` command? Tricky part: how to discover the current port number if the server is already running in another process? Perhaps store it in a local file in /tmp?

-   Disallow opening multiple CodeChat Clients for a given id. (This can only happen currently by manually entering a URL, so it's not a big problem.)


Ideas:

-   At the core of the design is a wrapped StringIO class that allows reads/writes from/to (dest, str) [e.g. (build_output, "...rendered x as JavaScript...")]. Opening this stream for reading returns an object that does blocking reads and remembers its location in the stream. It also offers a close_open method that, given an existing stream to close and a new stream to open, switches the blocking read being performed from the old to the new stream. StringIO also implements universal newlines.
-   The editor requests a render. The render manager either finds an existing render or creates a new render. For new renders, the render is enqueued. The render manager close_opens the web client's current render stream, replacing it with the new, resulting render stream; as a result, the web client then begins to read from this stream.
-   The render manager worker eventually dequeues the render then starts writing to it. The renderer writes output, then errors, then html.
-   The web client blocks on read until data is ready, then returns as much data as it can for each read.


Footnotes
=========
.. [#] At this time, only the Visual Studio Code editor is fully supported; there is basic support for Vim and most editors. See `../extensions/contents`.