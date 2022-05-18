# Constants



These constants are defined for use in Joomla and [extensions](https://docs.joomla.org/Extensions):-

| JPATH_ADMINISTRATOR           | The path to the administrator folder.                        |
| ----------------------------- | ------------------------------------------------------------ |
| JPATH_BASE                    | The path to the installed Joomla! site, or JPATH_ROOT/administrator if executed from the backend. |
| JPATH_CACHE                   | The path to the cache folder.                                |
| JPATH_COMPONENT               | The path to the current component being executed.            |
| JPATH_COMPONENT_ADMINISTRATOR | The path to the administration folder of the current component being executed. |
| JPATH_COMPONENT_SITE          | The path to the site folder of the current component being executed. |
| JPATH_CONFIGURATION           | The path to folder containing the configuration.php file.    |
| JPATH_INSTALLATION            | The path to the installation folder.                         |
| JPATH_LIBRARIES               | The path to the libraries folder.                            |
| JPATH_PLUGINS                 | The path to the plugins folder.                              |
| JPATH_ROOT                    | The path to the installed Joomla! site.                      |
| JPATH_SITE                    | The path to the installed Joomla! site.                      |
| JPATH_THEMES                  | The path to the templates folder.                            |
| JPATH_XMLRPC                  | The path to the XML-RPC Web service folder.(1.5 only)        |

These constants are defined in `_path_/includes/defines.php` except JPATH_BASE which is defined in `_path_/index.php`.

Note: These paths are the absolute paths of these locations within the file system, NOT the path you'd use in a URL.

For URL paths, try using [JURI::base()](https://docs.joomla.org/index.php?title=JURI/base&action=edit&redlink=1).

## Difference between JPATH_SITE, JPATH_ROOT, and JPATH_BASE

*JPATH_SITE* is meant to represent the root path of the JSite application, just as *JPATH_ADMINISTRATOR* is mean to represent the root path of the JAdministrator application.

*JPATH_BASE* is the root path for the current requested application.... so if you are in the administrator application:



If you are in the site application:



If you are in the installation application:



JPATH_ROOT is the root path for the Joomla install and does not depend upon any application.

## Consideration

Whilst using JPATH_COMPONENT and JPATH_COMPONENT_ADMINISTRATOR is highly useful in some cases, it has one big disadvantage: it immediately breaks all attempts to reuse the model from another component. That's something to keep in mind.