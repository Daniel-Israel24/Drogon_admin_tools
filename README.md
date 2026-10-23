
````markdown
# Drogon Admin Toolkit

A collection of Python utilities for generating, managing, and maintaining administration interfaces for C++ applications built with [Drogon](https://github.com/drogonframework/drogon).

The toolkit automates repetitive development tasks, including ORM model discovery, administration controller generation, CSP template generation, dashboard creation, route scanning, single-file packaging, and C++ include-extension repair.

> **Important:** Generated code must be reviewed, compiled, and tested against your Drogon version and project configuration before production use.

---

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
- [Project Structure](#project-structure)
- [Toolkit Components](#toolkit-components)
- [Installation and Setup](#installation-and-setup)
- [Recommended Workflow](#recommended-workflow)
- [admin.py](#adminpy--main-admin-generator)
- [admin_gen_ctrl.py](#admin_gen_ctrlpy--controller-generator)
- [admin_gen.py](#admin_genpy--supporting-admin-generator)
- [admin_gen_ui.py](#admin_gen_uipy--admin-ui-generator)
- [router.py](#routerpy--route-scanner-and-dashboard)
- [pack.py](#packpy--single-file-packager)
- [fix_includes.py](#fix_includespy--include-extension-repair)
- [Drogon Integration](#drogon-integration)
- [Security Considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)
- [Development Guidelines](#development-guidelines)

---

## Overview

The Drogon Admin Toolkit simplifies the creation and maintenance of administrative interfaces for applications that use Drogon ORM and its HTTP controller framework.

Rather than repeatedly writing controllers, view templates, dashboards, and supporting code manually, developers can use the toolkit to generate an initial implementation from existing project files and model metadata.

The toolkit consists of seven utilities:

| Utility | Responsibility |
|---|---|
| `admin.py` | Main administration-generation entry point |
| `admin_gen_ctrl.py` | Generates model-specific administration controllers |
| `admin_gen.py` | Supporting admin-generation utility |
| `admin_gen_ui.py` | Generates the shared administration UI |
| `router.py` | Scans routes and generates a route dashboard |
| `pack.py` | Packages the toolkit into one self-extracting Python file |
| `fix_includes.py` | Repairs incorrect C++ include-file extensions |

The utilities can be used together or independently, depending on the requirements of the project and the interfaces implemented by each script.

## Requirements

- Python installed and available from the command line.
- A working C++ application using Drogon.
- Drogon ORM models for model-based generation.
- A configured CMake build system or equivalent.
- A configured mechanism for compiling or loading Drogon CSP views.
- A terminal opened at the project root, unless explicit paths are supplied.

The Python utilities generate or modify files. They do not automatically guarantee that the resulting C++ compiles, links, or runs correctly.

Inspect the command-line options supported by your local scripts:

```bash
python admin.py --help
python admin_gen_ctrl.py --help
python admin_gen.py --help
python admin_gen_ui.py --help
python router.py --help
python pack.py --help
python fix_includes.py --help
````

If a script does not support `--help`, inspect its usage instructions and argument parser.

### Project Structure

An illustrative project layout is shown below. Actual filenames and directories depend on the configuration of your generators.

```text
my-drogon-project/
├── CMakeLists.txt
├── config.json
│
├── models/
│   ├── User.h
│   ├── User.cc
│   ├── Product.h
│   └── Product.cc
│
├── controllers/
│   ├── AdminDashboard.h
│   ├── AdminDashboard.cc
│   ├── AdminUserCtrl.h
│   ├── AdminUserCtrl.cc
│   ├── RouterCtrl.h
│   └── RouterCtrl.cc
│
├── views/
│   ├── AdminDashboardView.csp
│   ├── RouterView.csp
│   └── dash/
│       ├── User.csp
│       └── Product.csp
│
├── static/
│   └── admin/
│       ├── css/
│       ├── js/
│       └── images/
│
├── generated/
│   └── admin/
│       └── models.json
│
├── route_report/
│   ├── routes.json
│   └── routes_dashboard.html
│
├── admin.py
├── admin_gen.py
├── admin_gen_ctrl.py
├── admin_gen_ui.py
├── router.py
├── pack.py
└── fix_includes.py
```

### Toolkit Components

### 1\. `admin.py` — Main Admin Generator

### Purpose

`admin.py` is the main entry point for the administration-generation workflow.

Depending on its implementation, it may coordinate model discovery, create a model manifest, and initiate generation of controllers, views, and supporting assets.

### Typical responsibilities

* Discovering ORM model files.

* Extracting model names and metadata.

* Creating or updating a model manifest.

* Coordinating controller generation.

* Coordinating shared dashboard generation.

* Managing output locations and regeneration behavior.

### Model manifest

The toolkit may use a JSON manifest to describe the models discovered in the project.

An illustrative location is:

```text
generated/admin/models.json
```

The manifest can provide downstream generators with the information needed to generate controllers and views without independently rediscovering every model.

The exact JSON schema must match the schema expected by your scripts.

### Common configuration concepts

| Configuration         | Purpose                                        |
| --------------------- | ---------------------------------------------- |
| Models directory      | Directory containing ORM model files           |
| Output directory      | Root directory for generated artifacts         |
| Manifest path         | Location of model metadata                     |
| Relationship metadata | Additional model relationship information      |
| Force mode            | Allows existing generated files to be replaced |
| Skip-controller mode  | Skips controller generation where supported    |

These are configuration concepts. The actual command-line flags and defaults must be checked against the local implementation.

### Example

```bash
python admin.py
```

If supported by the script, explicit paths may be supplied:

```bash
python admin.py --models ./models --output .
```

Use only the options implemented by your version.

### When to use it

Use the main generator when:

* Setting up the admin interface.

* Adding new ORM models.

* Refreshing model metadata.

* Regenerating the overall administration structure.

### 2\. `admin_gen_ctrl.py` — Controller Generator

### Purpose

`admin_gen_ctrl.py` generates administration controllers and corresponding CSP templates for individual ORM models.

For example, a model named `User` may produce a controller named `AdminUserCtrl`.

### Illustrative generated files

```text
controllers/
├── AdminUserCtrl.h
├── AdminUserCtrl.cc
├── AdminProductCtrl.h
└── AdminProductCtrl.cc

views/
└── dash/
    ├── User.csp
    └── Product.csp
```

The exact filenames and class names depend on the generator's naming conventions.

### Controller responsibilities

Depending on the generated implementation, model controllers may provide:

* A model administration page.

* An endpoint to retrieve records.

* An endpoint to retrieve an individual record.

* An endpoint to delete a record.

* JSON responses for dashboard interactions.

* Database operations through Drogon ORM.

Inspect the generated source to confirm which operations are implemented.

### ORM considerations

Generated code must use the correct model class, primary-key type, and ORM APIs for the installed Drogon version.

Before compiling, verify:

* Model class names are correct.

* Model header paths exist.

* Primary-key types match the generated models.

* Database operations use the correct model.

* Asynchronous callbacks handle errors appropriately.

* Route paths and HTTP methods are correct.

* Generated `.cc` files are included in the application build.

### Example

If supported by the script, controller generation may resemble:

```bash
python admin_gen_ctrl.py \
  --manifest generated/admin/models.json \
  --output .
```

Adapt line continuation syntax for your shell or place the command on one line.

Check the local script for its actual supported arguments.

### Overwrite behavior

Before regenerating controllers, back up any manually modified files.

If a force option is supported, it may replace existing generated files and overwrite local modifications.

### 3\. `admin_gen.py` — Supporting Admin Generator

### Purpose

`admin_gen.py` performs a supporting task in the administration-generation workflow.

Its exact responsibility depends on the implementation in the local toolkit.

### Inspecting usage

```bash
python admin_gen.py --help
```

If help is unavailable, inspect the argument parser and main entry point.

### Before running it

Determine:

* Which input files it reads.

* Whether it depends on `models.json`.

* Which directories it writes to.

* Whether it generates controllers, views, metadata, or other assets.

* Whether it overwrites existing files.

* Whether another generator must run first.

### Recommended practice

Use `admin_gen.py` for the generation task it implements in your version of the toolkit.

Avoid assuming that it duplicates the responsibilities of `admin.py` or `admin_gen_ctrl.py` without inspecting the source.

Document changes to its interface whenever its behavior is modified.

### 4\. `admin_gen_ui.py` — Admin UI Generator

### Purpose

`admin_gen_ui.py` generates the shared administration interface.

Depending on the implementation, it may generate:

* A dashboard CSP template.

* Navigation components.

* Model links.

* CSS and JavaScript assets.

* Supporting dashboard resources.

Separating UI generation from controller generation allows the dashboard to be refreshed without necessarily regenerating every model controller.

### Illustrative outputs

```text
views/
└── AdminDashboardView.csp

static/
└── admin/
    ├── css/
    │   └── admin.css
    ├── js/
    │   └── admin.js
    └── images/
```

Use the filenames actually generated by your script.

### CSP view naming

Drogon normally renders a view by its registered view name rather than by its filename including `.csp`.

If the template is named:

```text
views/AdminDashboardView.csp
```

the controller would typically render it using:

```cpp
auto response =
    drogon::HttpResponse::newHttpViewResponse("AdminDashboardView");
```

If the file is named `AdminDashboard.csp`, the corresponding view name is normally `"AdminDashboard"`.

Keep the generated filename and the controller's view-name string consistent.

Renaming a template alone may not be sufficient if the controller or generator still refers to the old view name.

### Static assets

The dashboard may depend on CSS and JavaScript files under `static/admin/`.

For example, if the Drogon configuration maps `/admin/static/` to `static/admin`, a request for:

```text
/admin/static/admin.css
```

must resolve to the corresponding file under the configured filesystem root.

Check:

* `document_root` in `config.json`.

* Aliases for `/static/` and `/admin/static/`.

* The application's working directory.

* The generated asset filenames.

* Browser network requests for missing resources.

### Example

```bash
python admin_gen_ui.py --help
```

Use the supported options to generate or refresh the dashboard.

### 5\. `router.py` — Route Scanner and Dashboard

### Purpose

`router.py` scans C++ source and header files for recognizable Drogon route registrations.

It produces a route report and a dashboard for inspecting the routes discovered in the source code.

This is useful for development, debugging, and understanding an application's HTTP endpoint structure.

### Recognized route patterns

The scanner is designed to identify common patterns such as:

* `METHOD_ADD`

* `ADD_METHOD_TO`

* `PATH_ADD`

* `registerHandler`

Static scanning cannot guarantee that every runtime route will be discovered. Dynamically constructed routes or unsupported registration patterns may be missed.

### Command-line options

The described implementation uses options including:

| Option  | Purpose                                                               |
| ------- | --------------------------------------------------------------------- |
| --root  | Project/source root to scan                                           |
| --out   | Directory for route report output                                     |
| --force | Allows replacement of generated view/controller files where supported |

The commonly used defaults are `.` for the scan root and `./route_report` for the report directory.

### Basic usage

```bash
python router.py --root . --out ./route_report
```

If the generator refuses to overwrite existing files, inspect them first and then use force mode if supported:

```bash
python router.py --root . --out ./route_report --force
```

### Generated files

```text
views/
└── RouterView.csp

controllers/
├── RouterCtrl.h
└── RouterCtrl.cc

route_report/
└── routes.json
```

The report directory may also contain an HTML dashboard, depending on the generator version.

### Dashboard features

The generated dashboard may provide:

* A list of discovered routes.

* Route search.

* Filtering by HTTP method or controller.

* Route statistics.

* Links to route paths.

* JSON export of route data.

The precise features depend on the generated template.

### Serving the dashboard at `/`

The generated controller can register the dashboard at the root route:

```cpp
ADD_METHOD_TO(RouterCtrl::index, "/", drogon::Get);
```

The handler typically renders the `RouterView` template:

```cpp
auto response =
    drogon::HttpResponse::newHttpViewResponse("RouterView");
```

The view name must match the template registered with Drogon.

Check for an existing root route before enabling this controller. If another controller already handles `/`, choose which controller should own that route or change the generated route path.

### Build integration

After generating the files:

* Ensure `RouterCtrl.cc` is included in the application's CMake target.

* Ensure `RouterView.csp` is compiled or registered as a Drogon view.

* Confirm that the route is registered at the expected path.

* Rebuild and restart the application.

* Open the dashboard in a browser.

* Compare the dashboard results with the source code and runtime behavior.

The route scanner is a development aid, not a complete security audit.

### 6\. `pack.py` — Single-File Packager

### Purpose

`pack.py` packages the toolkit directory into a single Python file.

The resulting file is intended to be executable: when run with Python, it unpacks the bundled content and recreates the directory structure and files.

This makes it easier to transfer or archive the toolkit as one artifact.

### General workflow

* Prepare the directory containing the scripts and supporting files.

* Decide which files should be included.

* Run `pack.py` using the usage supported by the local implementation.

* Keep the resulting single-file package.

* Transfer the package to the target machine.

* Execute it with Python to restore the bundled files.

* Compare the restored directory with the original.

### Inspecting usage

```bash
python pack.py --help
```

If the script does not support `--help`, inspect its source for the expected arguments and output filename.

### Suggested contents

```text
admin.py
admin_gen.py
admin_gen_ctrl.py
admin_gen_ui.py
router.py
fix_includes.py
```

Include any supporting Python modules, templates, or resources required by the toolkit.

### Files to exclude

Unless specifically needed, avoid bundling:

* `.git/` history.

* Python virtual environments.

* `__pycache__/` directories.

* Build directories and compiled binaries.

* Temporary output.

* Large generated reports.

* Local databases containing real data.

* Credentials, passwords, and API keys.

* Unrelated user files.

### Restoring the directory

If the packer generates a file named `drogon_admin_bundle.py`, it might be executed as follows:

```bash
python drogon_admin_bundle.py
```

The actual filename and invocation depend on the packer's implementation.

Before running the unpacker in a directory containing important files, inspect how it handles existing paths and whether it overwrites files.

### Verification

After extraction:

* Compare the directory structure.

* Check important file contents.

* Compare hashes if byte-for-byte restoration is required.

* Run Python syntax checks.

* Execute a harmless test invocation.

A self-extracting Python file is still executable code. Only run packages from trusted sources.

### 7\. `fix_includes.py` — Include Extension Repair

### Purpose

`fix_includes.py` corrects file-extension mismatches in C++ include directives.

It is useful when a source file references a header extension that differs from the actual filename on disk.

### Example

A source file might contain:

```cpp
#include "models/User.hpp"
```

while the actual header is:

```text
models/User.h
```

If the script's repair rules recognize the mismatch, it can update the include directive to use the correct extension.

### Recommended workflow

* Confirm the actual filenames on disk.

* Inspect the script's supported options.

* Run the utility against the intended source tree.

* Review the modified include directives.

* Build the application and confirm the errors are resolved.

Inspect the command-line interface:

```bash
python fix_includes.py --help
```

If the script does not support help output, consult its usage instructions.

### Limitations

Correcting an extension does not guarantee that:

* The include path is valid.

* The include directory is configured in CMake.

* The file declares the expected class or namespace.

* The C++ code is valid.

* The project will compile successfully.

System headers such as:

```cpp
#include <string>
#include <drogon/drogon.h>
```

should not be treated as ordinary project headers.

### Review changes

Inspect the changes made by the utility before committing them. Ensure it modifies only the intended include directives and that all resulting paths refer to real files.

### Drogon Integration

Generating files is only one part of integrating the administration interface into a Drogon application.

### Compile generated controllers

Ensure generated `.cc` files are included in the application build.

If `CMakeLists.txt` explicitly lists source files, add the generated files to the appropriate target or use the project's existing source-discovery mechanism.

### Compile or register CSP templates

Creating a `.csp` file does not automatically guarantee that Drogon can render it.

Depending on the project configuration, CSP templates may be compiled during the build or loaded through a configured dynamic-view mechanism.

Verify:

* The project's Drogon view-generation setup.

* Whether dynamic view loading is enabled.

* The configured view directory.

* The view names requested by controllers.

* Whether a rebuild is required after changing templates.

### Keep view names consistent

| CSP filename           | Typical view name  |
| ---------------------- | ------------------ |
| RouterView.csp         | RouterView         |
| AdminDashboardView.csp | AdminDashboardView |
| User.csp               | User               |

Use the view name expected by your Drogon configuration. Avoid including `.csp` in `newHttpViewResponse()` unless your custom loading mechanism explicitly requires it.

### Verify static paths

For example:

```text
URL:        /admin/static/app.css
Filesystem: static/admin/app.css
```

This mapping is valid only if the configured static route and alias map the URL to that filesystem location.

### Rebuild and restart

After adding controllers or changing templates:

* Reconfigure the build if necessary.

* Compile the application.

* Resolve compiler and linker errors.

* Restart the application.

* Test the generated routes and views.

* Inspect application logs and browser network requests.

### Generated Routes

The exact routes depend on the generated controller source.

A typical administration design may use patterns such as:

| Route pattern            | Intended purpose                         |
| ------------------------ | ---------------------------------------- |
| /admin/<model>/          | Administration page for a model          |
| /api/<model>/            | Model API endpoint, if generated         |
| /api/<model>/<id>        | Individual record endpoint, if generated |
| /api/<model>/<id>/delete | Delete endpoint, if generated            |

These are illustrative patterns, not a guarantee that every controller uses the same paths, HTTP methods, or response formats.

Inspect the generated route registrations to determine the actual behavior.

The route dashboard generated by `router.py` is separate from the model administration routes. If it is registered at `/`, verify that the path is not already assigned to another handler.

### Security Considerations

An administration interface can expose sensitive records and destructive operations. Do not assume that generated code is secure by default.

### Authentication

Require authentication for administrative pages and endpoints.

### Authorization

Check user permissions on every protected request. Hiding a button in the interface is not access control.

### Destructive actions

Protect delete operations and other state-changing endpoints. Validate identifiers, check permissions, and handle missing records safely.

### Input validation

Validate route parameters, query strings, and request bodies before using them in database operations.

### CSRF protection

If the application uses cookie-based authentication, implement appropriate CSRF protection for state-changing requests.

### Cross-site scripting

Escape or encode untrusted database values before rendering them in HTML or CSP templates. Avoid inserting untrusted values directly into JavaScript or HTML contexts.

### Database errors

Handle ORM errors and database failures without exposing credentials, internal paths, SQL details, or sensitive implementation information.

### Transport security

Use HTTPS when credentials or sensitive records are transmitted over untrusted networks.

### Logging

Avoid logging passwords, access tokens, and unnecessary sensitive record contents.

### Production deployment

Before exposing the admin interface over a network, review authentication, authorization, network exposure, database permissions, and deployment configuration.

The generators do not independently guarantee these security controls. Implement and test them in the application.

### Troubleshooting

### The dashboard returns 404

Check that:

* The controller source is compiled into the running application.

* The route registration matches the requested path.

* Another controller has not claimed the same path.

* The application has been rebuilt and restarted.

### Drogon cannot find a CSP view

Check that:

* The CSP file exists in the expected directory.

* The template has been compiled, registered, or loaded.

* The controller uses the correct view name.

* The build configuration includes the relevant view-generation step.

### CSS or JavaScript returns 404

Check that:

* The requested URL is correct.

* The static route prefix matches the URL.

* The configured alias points to the correct directory.

* The file exists at the resolved filesystem path.

* The application's working directory is correct when relative paths are used.

### A generated controller is missing

Check that:

* The generator completed successfully.

* The output directory is correct.

* The file was not skipped because it already existed.

* The generated `.cc` file is included in the build target.

### Include errors persist

Check that:

* The filename and extension are correct.

* The include path is valid.

* The include directory is configured in CMake.

* The referenced file exists.

* The header declares the expected types.

### A route is missing from the report

Check that:

* The correct source root was scanned.

* The registration uses a pattern supported by the scanner.

* The route is not constructed dynamically.

* The route registration is not hidden behind an unsupported macro or helper.

### Regeneration did not update a file

Some generators skip existing files unless force mode is enabled. Inspect the script's behavior and use force mode only after reviewing the files that would be replaced.

### Development Guidelines

### Keep generated code separate from hand-written code

Where possible, keep custom application logic in separate files so regeneration does not overwrite it.

### Use version control

Commit the generator source and review generated-file diffs before committing the generated artifacts.

### Test generated code

A successful Python run confirms that the generator executed; it does not prove that the generated C++ compiles or that the views work.

### Keep documentation synchronized

When modifying a generator, update this README with:

* Its purpose.

* Required inputs.

* Supported command-line options.

* Output paths and naming conventions.

* Overwrite behavior.

* Dependencies on other generators.

* Changes to generated routes or templates.

### Verify packaging

After using `pack.py`, test extraction into a clean temporary directory and confirm that the restored tree contains the expected files.

### Review include repairs

After using `fix_includes.py`, inspect the diff and build the project.

### License and Attribution

Add the project's license, copyright notices, and third-party attribution requirements here before redistributing the toolkit.

### Conclusion

The Drogon Admin Toolkit brings together model discovery, controller generation, dashboard generation, route inspection, single-file packaging, and include repair.

Used carefully, these utilities can reduce repetitive development work and make a Drogon application's administration interface easier to maintain.

Always review generated artifacts, verify build integration, test runtime behavior, and apply appropriate security controls before deployment.
