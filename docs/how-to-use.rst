.. _How To Use This Plugin:

How To Use This Plugin
======================

Simple Usage (Single Code Cell or File)
---------------------------------------

Assuming you have installed this plugin correctly, extra commands will be registered under the section of :code:`JUPYTERLAB CODE FORMATTER` in the command palette, click on any of them to format whatever you were focused on.


Simple Usage (Selected Cells)
-----------------------------

Highlight cells you want to format, then right click and select :code:`Format Cells`.

.. important::

    This uses the default formatter defined in config. See :ref:`Changing Default Formatter` for more info.


Simple Usage (Selected All Cells)
---------------------------------

An extra button should appear in the toolbar (it should say :code:`Format Notebook` when hovering over it), click that and the whole notebook will be formatted where possible.

.. important::

    This uses the default formatter defined in config. See :ref:`Changing Default Formatter` for more info.

Keyboard Shortcuts
------------------

Scrolling down the command palette or clicking with the mouse is not efficient at all. To add to the available shortcuts, add something like the below to the shortcut section of the Advanced Setting Edtior of Jupyterlab.

.. code-block:: json

    {
        "shortcuts": [
            {
                "command": "jupyterlab_code_formatter:black",
                "keys": [
                    "Ctrl K",
                    "Ctrl M"
                ],
                "selector": ".jp-Notebook.jp-mod-editMode"
            }
        ]
    }

The above example breaks down to

    - Under edit mode (detected through the selector);
    - Using the chord :code:`Ctrl+K Ctrl+M`;
    - Invoke the :code:`jupyterlab_code_formatter:black` command;


Other Available Commands
------------------------

To find out what formatters are available, you can query http://localhost:8888/jupyterlab_code_formatter/formatters (you might need to replace the port and address), the keys of formatter are shown there.

To bind the format selected cells/format all cells command, the command to use would be :code:`jupyterlab_code_formatter:format` and :code:`jupyterlab_code_formatter:format_all` respectively.


.. _Changing Default Formatter:

Changing Default Formatter
--------------------------

To change the default formatter used the format action in context menu/toolbar (also :code:`jupyterlab_code_formatter:format` and
:code:`jupyterlab_code_formatter:format_all`), go to "Settings" > "Advanced Settings Editor" > "Jupyterlab Code Formatter", then in the "User Preferences" panel, enter, for example:

.. code-block:: json

    {
        "preferences": {
            "default_formatter": {
                "python": "autopep8",
                "R": "styler"
            }
        }
    }

Chaining Default Formatters One After The Other
```````````````````````````````````````````````

To invoke more than one formatters with invoking the commands :code:`jupyterlab_code_formatter:format` and
:code:`jupyterlab_code_formatter:format_all`, one could configure the default formatter to be an array of strings like so:

.. code-block:: json

    {
        "preferences": {
            "default_formatter": {
                "python": ["isort", "black"],
                "R": ["styler", "formatR"],
            }
        }
    }

Changing Formatter Parameter
----------------------------

There are also some formatter config exposed through the Jupyter Lab Advanced Settings Editor, have a browse and change it if you wish. for example:

.. code-block:: json

    {
        "autopep8": {
            "max_line_length": 120,
            "ignore": [
                "E226",
                "E302",
                "E41"
            ]
        }
    }

Styler Configuration Example
````````````````````````````

The :code:`list` construct is actually a JSON dictionary, to use :code:`math_token_spacing` and :code:`reindention` config, one would need to do something like the following.

.. code-block:: json

    {
        "styler": {
            "math_token_spacing": {
                "zero":["'^'"],
                "one":["'+'", "'-'", "'*'","'/'"]
            },
            "reindention": {
                "regex_pattern" : "^###",
                "indention" : 0,
                "comments_only" : true}
        }
    }


Auto Format On Save
-------------------

Go to Jupyter Lab Advanced Settings Editor, under :code:`Jupyterlab Code Formatter` section, include the following key value pair.

.. code-block:: json

    {
        "formatOnSave": true
    }


Custom Formatter
----------------

To define a custom formatter, you can do so in the Jupyter notebook configuration (usually found :code:`~/.jupyter/jupyter_notebook_config.py` or something along those lines), the following example adds a rather useless formatter as a example.

.. code-block:: python


    from jupyterlab_code_formatter.formatters import BaseFormatter, handle_line_ending_and_magic, SERVER_FORMATTERS

    class BruhFormatter(BaseFormatter):

        label = "Apply Bruh Formatter"

        @property
        def importable(self) -> bool:
            return True

        @handle_line_ending_and_magic
        def format_code(self, code: str, notebook: bool, **options) -> str:
            return (
                "# i am a notebook  # bruh\n"
                if (not code.startswith("# i am a notebook  # bruh") and notebook) else ""
            ) + "\n".join(
                [
                    line if line.endswith("# bruh") else f"{line}  # bruh"
                    for line in code.splitlines()
                ]
            )

    SERVER_FORMATTERS['bruh'] = BruhFormatter()


When implementing your customer formatter using third party library, you will likely use :code:`try... except` in the :code:`importable` block instead of always returning True.
