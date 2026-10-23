Drogon Admin Toolkit

A collection of Python utilities for generating, managing, and maintaining an administration interface for C++ applications built with "Drogon" (https://github.com/drogonframework/drogon).

The toolkit automates repetitive development tasks, including discovering ORM models, generating administration controllers and CSP views, building dashboard interfaces, scanning HTTP routes, packaging the tools into a single Python file, and repairing incorrect C++ include-file extensions.

«Important: Generated code is a starting point. Review, compile, and test it against your Drogon version and project configuration before using it in production.»

Table of Contents

- "Overview" (#overview)
- "Requirements" (#requirements)
- "Toolkit Components" (#toolkit-components)
- "Project Structure" (#project-structure)
- "Installation and Setup" (#installation-and-setup)
- "Recommended Workflow" (#recommended-workflow)
- "1. admin.py" (#1-adminpy--main-admin-generator)
- "2. admin_gen_ctrl.py" (#2-admin_gen_ctrlpy--controller-generator)
- "3. admin_gen.py" (#3-admin_genpy--supporting-admin-generator)
- "4. admin_gen_ui.py" (#4-admin_gen_uipy--admin-ui-generator)
- "5. router.py" (#5-routerpy--route-scanner-and-dashboard)
- "6. pack.py" (#6-packpy--single-file-packager)
- "7. fix_includes.py" (#7-fix_includespy--include-extension-repair)
- "Drogon Integration" (#drogon-integration)
- "Generated Routes" (#generated-routes)
- "Security Considerations" (#security-considerations)
- "Troubleshooting" (#troubleshooting)
- "Development Guidelines" (#development-guidelines)

Overview

The Drogon Admin Toolkit is designed to simplify the process of creating an administration interface for applications that use Drogon ORM and its HTTP controller framework.

Instead of repeatedly writing controller classes, view templates, dashboard pages, and supporting code manually, the toolkit provides generators that produce an initial implementation from existing project files and metadata.

The toolkit has seven main components:

Utility| Responsibility
"admin.py"| Main admin-generation entry point
"admin_gen_ctrl.py"| Generates model-specific administration controllers
"admin_gen.py"| Performs its supporting admin-generation task
"admin_gen_ui.py"| Generates the administration dashboard and UI
"router.py"| Scans registered routes and generates a route dashboard
"pack.py"| Packages the toolkit into one self-extracting Python file
"fix_includes.py"| Repairs incorrect extensions in C++ include directives

The scripts can be used together or individually, depending on the task and the interfaces implemented by each script.

Requirements

- Python installed and available from the command line.
- A working C++ project using Drogon.
- Drogon ORM model files, where model generation is required.
- A configured CMake build or equivalent build system.
- A terminal opened in the project directory, unless explicit paths are supplied.
- A configured Drogon view compilation or loading mechanism for CSP templates.

The generators create or modify files. They do not automatically guarantee that the generated application compiles, links, or runs correctly.

Inspecting command-line options

Where supported, use the following commands to inspect each utility's available options:

python admin.py --help
python admin_gen_ctrl.py --help
python admin_gen.py --help
python admin_gen_ui.py --help
python router.py --help
python pack.py --help
python fix_includes.py --help

If a script does not implement "--help", inspect its usage text and argument parser before running it.

Toolkit Components

1. Main generator

"admin.py" coordinates the administration-generation workflow, including model discovery and generation of the relevant artifacts.

2. Controller generator

"admin_gen_ctrl.py" creates model-specific controller source files, headers, and corresponding CSP views based on the metadata it receives.

3. Supporting generator

"admin_gen.py" handles an additional generation task within the toolkit. Its precise inputs and outputs depend on the implementation in the local copy.

4. UI generator

"admin_gen_ui.py" generates the shared administration interface and associated view or static assets.

5. Route scanner

"router.py" scans C++ files for recognized Drogon route registrations and produces a route report and a browser-based dashboard.

6. Packager

"pack.py" packages the selected toolkit directory into a single Python file that can recreate the bundled files when executed.

7. Include repair utility

"fix_includes.py" corrects include-file extension mismatches, such as an include referencing ".hpp" when the actual project header uses ".h".

Project Structure

A typical project using the toolkit may resemble the following:

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

This structure is illustrative. Actual paths and filenames depend on the generators' configurations.

Installation and Setup

Step 1: Place the utilities in your project

Keep the Python scripts in a dedicated toolkit directory or at the project root.

For example:

drogon-project/
└── tools/
    ├── admin.py
    ├── admin_gen.py
    ├── admin_gen_ctrl.py
    ├── admin_gen_ui.py
    ├── router.py
    ├── pack.py
    └── fix_includes.py

If you keep them under "tools/", ensure you execute them with the correct project paths.

Step 2: Verify the project

Confirm that the models, controller directories, view directories, and build configuration exist.

Step 3: Inspect the available options

Run the relevant script with "--help", if supported, before passing custom paths or overwrite flags.

Step 4: Generate the admin artifacts

Run the main generator or the individual generators required for your workflow.

Step 5: Build and test

Review the generated files, ensure they are included in the build, compile the application, and test the generated routes and views.

Recommended Workflow

A practical development workflow is:

1. Update or create your Drogon ORM models.
2. Update any model metadata required by the generator.
3. Run "admin.py" to discover models and generate the required artifacts.
4. Run "admin_gen_ctrl.py" if you need to regenerate model controllers separately.
5. Run "admin_gen_ui.py" if you need to refresh the shared dashboard.
6. Run "fix_includes.py" if generated include directives use incorrect extensions.
7. Build the Drogon application.
8. Run the application and test the admin pages and endpoints.
9. Run "router.py" to inspect the source-level route inventory.
10. Package the toolkit with "pack.py" when you need a portable distribution.

Do not run all generators blindly over the same files. First establish which files each script creates or overwrites.

---

1. "admin.py" — Main Admin Generator

Purpose

"admin.py" is the main entry point for generating the administration interface.

Depending on its implementation, it can coordinate model discovery, create a model manifest, and initiate generation of controllers, views, or supporting assets.

Typical responsibilities

- Discovering model files.
- Extracting model names and related metadata.
- Creating or updating the model manifest.
- Coordinating generation of model-specific controllers.
- Coordinating generation of shared dashboard components.
- Providing options to control output locations and regeneration behavior.

Model manifest

A generator may use a JSON manifest to store discovered models and metadata.

An illustrative location is:

generated/admin/models.json

A manifest can help downstream generators determine which models to process without rescanning the entire source tree.

The exact schema must match what your generators expect. Do not manually replace the manifest with a guessed schema.

Common configuration concepts

Option| Purpose
Models directory| Identifies the directory containing ORM models
Output directory| Specifies where generated artifacts are written
Manifest path| Specifies the model metadata file
Relationship metadata| Supplies model relationships where supported
Force mode| Allows existing generated files to be overwritten
Skip-controller mode| Skips controller generation where supported

These are configuration concepts, not a guarantee that every version of "admin.py" implements identically named command-line flags. Check the local script before using them.

Example

python admin.py

If the script supports explicit paths, a run might look like:

python admin.py --models ./models --output .

Use the actual options printed by your script's help or usage output.

When to use it

Use the main generator when:

- Setting up the admin interface for the first time.
- Adding new ORM models.
- Refreshing model metadata.
- Regenerating the overall administration structure.

---

2. "admin_gen_ctrl.py" — Controller Generator

Purpose

"admin_gen_ctrl.py" generates administration controllers and associated view templates for individual ORM models.

For example, a model called "User" may have a controller named "AdminUserCtrl".

Expected generated files

An illustrative output structure is:

controllers/
├── AdminUserCtrl.h
├── AdminUserCtrl.cc
├── AdminProductCtrl.h
└── AdminProductCtrl.cc

views/
└── dash/
    ├── User.csp
    └── Product.csp

The exact class names, directory names, and view filenames depend on the generator's naming conventions.

Controller responsibilities

Depending on the generated implementation, model controllers may provide:

- A model-specific administration page.
- A route to retrieve records.
- A route to retrieve an individual record.
- A route to delete a record.
- JSON responses for dashboard interactions.
- Database operations through Drogon ORM.

Always inspect the generated route registrations and handler implementations to confirm which operations are actually present.

ORM considerations

Drogon ORM models may expose their primary-key type through a trait such as:

using PrimaryKeyType = typename drogon::orm::Mapper<User>::Traits::PrimaryKeyType;

The exact type alias available depends on the Drogon version and ORM APIs used by the project. Generated code should use the primary-key declaration appropriate for the installed version.

Before compiling, verify:

- The model class name is correct.
- The included model header exists.
- The primary-key type matches the model.
- Database operations use the correct table and model.
- Asynchronous callbacks handle errors.
- The controller's route and HTTP method are correct.
- Generated source files are part of the application target.

Example

If the script supports a manifest and output path, usage may resemble:

python admin_gen_ctrl.py \
  --manifest generated/admin/models.json \
  --output .

On Windows PowerShell or Command Prompt, adapt line continuation to the shell or put the command on one line.

Check the local script for the exact accepted arguments.

Overwriting generated controllers

Before regenerating a controller, preserve any manual modifications.

If a force option is supported, it may replace existing files and destroy edits that were not moved into separate hand-written files.

---

3. "admin_gen.py" — Supporting Admin Generator

Purpose

"admin_gen.py" is another component of the admin-generation workflow.

Its exact responsibility is determined by the local implementation. It may provide an additional generation stage or coordinate a subset of the overall process.

Usage

First inspect the available options:

python admin_gen.py --help

If help is not supported, inspect the script's argument parser and entry point.

Before running it

Establish the following:

- Which input files it reads.
- Whether it depends on "models.json".
- Which output directories it writes to.
- Whether it generates controllers, views, metadata, or other assets.
- Whether it overwrites existing files.
- Whether another generator must run first.

Recommended practice

Run "admin_gen.py" as part of the documented workflow for your version of the toolkit. Avoid assuming it duplicates the responsibilities of "admin.py" or "admin_gen_ctrl.py" without checking the implementation.

Document changes to its command-line interface whenever its behavior is modified.

---

4. "admin_gen_ui.py" — Admin UI Generator

Purpose

"admin_gen_ui.py" generates the shared administration interface.

Depending on the implementation, this may include a dashboard CSP template, navigation elements, model links, JavaScript, CSS, and supporting static assets.

Separating UI generation from controller generation allows the dashboard to be updated without necessarily regenerating every model controller.

Potential outputs

An illustrative structure is:

views/
└── AdminDashboardView.csp

static/
└── admin/
    ├── css/
    │   └── admin.css
    ├── js/
    │   └── admin.js
    └── images/

Use the filenames actually generated by your script.

View naming and ambiguity

Drogon view rendering normally refers to a view by its registered view name rather than the complete filename with the ".csp" suffix.

If the template is named:

views/AdminDashboardView.csp

the corresponding controller typically renders it using:

auto response =
    drogon::HttpResponse::newHttpViewResponse("AdminDashboardView");

If the template is named "AdminDashboard.csp", the corresponding view name is normally ""AdminDashboard"".

Keep the filename and the view name consistent. Renaming the file alone may not be sufficient if a controller or generator still refers to the previous name.

Static asset integration

The dashboard may load CSS and JavaScript from the configured static directory.

For example, if your Drogon configuration maps "/admin/static/" to "static/admin", then:

Browser URL:  /admin/static/admin.css
Filesystem:   static/admin/admin.css

This is only an example; the URL and path must match your actual configuration and asset filenames.

Check:

- "document_root" in "config.json".
- The aliases for "/static/" and "/admin/static/".
- The application's working directory.
- The actual location of the generated files.
- The browser's network panel for missing resources.

Example

python admin_gen_ui.py --help

Use the supported options from your local script to generate or refresh the dashboard.

---

5. "router.py" — Route Scanner and Dashboard

Purpose

"router.py" scans C++ source and header files for recognizable Drogon route-registration patterns.

It generates a route report and a dashboard for inspecting the routes found in source code.

This is useful for development, debugging, and understanding an application's HTTP endpoint structure.

Recognized route patterns

The scanner is designed to identify common patterns such as:

- "METHOD_ADD"
- "ADD_METHOD_TO"
- "PATH_ADD"
- "registerHandler"

Static scanning cannot guarantee that every runtime route will be discovered. Routes built dynamically, registered indirectly, or written in unsupported patterns may be missed.

Command-line options

The version described by this toolkit uses options including:

Option| Purpose
"--root"| Project/source root to scan
"--out"| Output directory for the route report
"--force"| Allows replacement of existing generated view/controller files, where supported

The commonly used defaults are "." for the scan root and "./route_report" for the report directory.

Basic usage

Run the scanner from the project root:

python router.py --root . --out ./route_report

If the generator refuses to overwrite existing generated files and you have reviewed the changes:

python router.py --root . --out ./route_report --force

Generated files

The generated artifacts include:

views/
└── RouterView.csp

controllers/
├── RouterCtrl.h
└── RouterCtrl.cc

route_report/
└── routes.json

The report directory may also contain an HTML dashboard, depending on the generator version.

Router dashboard features

The generated dashboard can provide features such as:

- Displaying discovered routes.
- Searching route paths and related information.
- Filtering by HTTP method or controller.
- Displaying route statistics.
- Opening route URLs.
- Exporting filtered route data as JSON.

The exact features depend on the generated template version.

Serving the dashboard at "/"

The generated "RouterCtrl" can register the dashboard at the root route:

ADD_METHOD_TO(RouterCtrl::index, "/", drogon::Get);

The handler typically returns the "RouterView" template:

auto response =
    drogon::HttpResponse::newHttpViewResponse("RouterView");

The view name must match the CSP template registered with Drogon.

Check for an existing root route before enabling this controller. If another controller already handles "/", choose which controller should own that route or change the generated route path.

Build integration

After generating the files:

1. Ensure "RouterCtrl.cc" is included in the application's CMake target.
2. Ensure "RouterView.csp" is compiled or registered as a Drogon view.
3. Confirm the route is registered at the expected path.
4. Rebuild and restart the application.
5. Open the root URL in a browser.
6. Compare the displayed route list with the source code and runtime behavior.

The route scanner is an inspection tool, not a complete security or authorization audit.

---

6. "pack.py" — Single-File Packager

Purpose

"pack.py" packages the toolkit directory into one Python file.

The resulting file is intended to be executable: when run with Python, it unpacks the bundled content and recreates the directory structure and files.

This makes it easier to transfer or archive a collection of scripts as one artifact.

General workflow

1. Prepare the directory containing the scripts and supporting files.
2. Decide which files should be included.
3. Run "pack.py" using the usage supported by the local implementation.
4. Keep the resulting single-file package.
5. Transfer the package to the target machine.
6. Run it with Python to restore the bundled files.
7. Compare the restored directory with the original.

Inspecting usage

python pack.py --help

If the script does not support "--help", inspect its source for the expected arguments and output filename.

What should be included?

Typical files to package include:

admin.py
admin_gen.py
admin_gen_ctrl.py
admin_gen_ui.py
router.py
fix_includes.py

Include any other Python modules, templates, configuration examples, or supporting resources required for the toolkit to work.

What should normally be excluded?

Unless specifically needed, avoid bundling:

- ".git/" history.
- Python virtual environments.
- "__pycache__/" directories.
- Build directories and compiled binaries.
- Temporary output.
- Large generated reports.
- Local databases containing real data.
- Credentials, passwords, API keys, or private configuration.
- User-specific files unrelated to the toolkit.

Restoring the directory

Run the generated package using Python according to its intended interface.

For example, if the package is named "drogon_admin_bundle.py":

python drogon_admin_bundle.py

The actual filename depends on the packer's output configuration.

Before running a self-extracting script in a directory containing important work, inspect how it handles existing files. It may overwrite paths or recreate files according to the packer's implementation.

Verification

After extraction, verify the restored contents by:

- Comparing the directory tree.
- Checking important file contents.
- Comparing hashes if exact byte-for-byte restoration is required.
- Running Python syntax checks on the restored scripts.
- Executing a harmless test invocation.

A single-file package is still executable code. Only run packages from trusted sources.

---

7. "fix_includes.py" — Include Extension Repair

Purpose

"fix_includes.py" corrects file-extension mismatches in C++ include directives.

This is useful when a header file has been renamed or generated with a different extension from the one referenced in a source file.

Example problem

A source file may contain:

#include "models/User.hpp"

while the actual file on disk is:

models/User.h

If the script recognizes this mismatch and its repair rules permit it, it can update the include to reference the correct extension.

Recommended workflow

1. Confirm the actual filenames on disk.
2. Inspect the script's supported options.
3. Run the utility against the intended source tree.
4. Review the resulting changes.
5. Build the application and verify that the include errors are resolved.

Inspect the command-line interface with:

python fix_includes.py --help

If no help option is implemented, consult the script's usage instructions.

What the tool does not guarantee

Correcting a file extension does not guarantee that:

- The header is in a configured include directory.
- The file declares the expected class or namespace.
- The include path is correct relative to the source file.
- The C++ code is valid.
- The application will compile successfully.

System headers such as:

#include <string>
#include <drogon/drogon.h>

should not be changed as if they were ordinary project headers.

Review before committing

Inspect changes made by the utility. A repair script should modify only the intended include directives, and changes should be tested against the real project structure.

---

Drogon Integration

Generating files is only one part of integrating the admin interface into a Drogon application.

1. Compile the generated controllers

Ensure generated ".cc" files are included in the application build.

For example, if your "CMakeLists.txt" explicitly lists source files, add the new controller files to the appropriate target.

The precise CMake changes depend on how the project discovers and compiles its sources.

2. Compile or register CSP templates

Creating a ".csp" file does not automatically guarantee that Drogon can render it.

Depending on the project's setup, CSP templates may be compiled during the build or loaded through a configured dynamic-view mechanism.

Check:

- The project's Drogon view-generation setup.
- Whether dynamic view loading is enabled.
- The configured view directory.
- The view names used by controllers.
- Whether a rebuild is required after modifying templates.

3. Keep view names consistent

For example:

CSP filename| Typical view name
"RouterView.csp"| "RouterView"
"AdminDashboardView.csp"| "AdminDashboardView"
"User.csp"| "User"

Use the view name expected by the project's Drogon view setup. Avoid including ".csp" in "newHttpViewResponse()" unless your own custom loading mechanism explicitly requires it.

4. Verify static files

If a dashboard loads external CSS or JavaScript, confirm the browser URL maps to the correct filesystem location.

For example:

URL:        /admin/static/app.css
Filesystem: static/admin/app.css

This mapping is valid only if the Drogon configuration maps the URL prefix to that directory.

5. Rebuild and restart

After adding controllers or changing CSP templates:

1. Reconfigure the build if necessary.
2. Compile the application.
3. Resolve compiler and linker errors.
4. Restart the application.
5. Test the routes and view rendering.
6. Inspect application logs and browser network requests.

---

Generated Routes

The actual routes depend on the generated controller source. A typical administration design may use route patterns like these:

Route pattern| Intended purpose
"/admin/<model>/"| Administration page for a model
"/api/<model>/"| Model API endpoint, if generated
"/api/<model>/<id>"| Individual record endpoint, if generated
"/api/<model>/<id>/delete"| Delete endpoint, if generated

These are illustrative patterns, not a guarantee that every controller uses these exact paths, methods, or response formats.

Always inspect the route registrations in the generated source. In Drogon, route paths, HTTP methods, and handler signatures must agree.

The route dashboard generated by "router.py" is separate from the model administration routes. If it is registered at "/", make sure that path is not already assigned to another handler.

Security Considerations

An administration interface can expose sensitive records and destructive operations. Do not assume that a generated dashboard is secure by default.

Authentication

Require authentication for administrative pages and APIs.

Authorization

Check the current user's permissions on every protected request. Hiding a button in the UI is not authorization.

Destructive actions

Protect delete operations and other state-changing endpoints. Verify permissions, validate identifiers, and handle missing records safely.

Input validation

Validate route parameters, query strings, and request bodies before using them in database operations.

Cross-site request forgery

If the application uses cookie-based authentication, evaluate and implement appropriate CSRF protection for state-changing requests.

Cross-site scripting

Escape or encode untrusted database values before rendering them in HTML or CSP templates. Avoid inserting untrusted values directly into JavaScript or HTML contexts.

Database errors

Handle ORM errors and database failures without exposing internal paths, credentials, SQL details, or other sensitive implementation information.

Transport security

Use HTTPS when credentials or sensitive records are transmitted over untrusted networks.

Logging

Avoid writing passwords, access tokens, and unnecessary sensitive record contents to logs.

Production deployment

Before making the admin interface accessible over a network, review authentication, authorization, network exposure, database permissions, and deployment configuration.

The generators do not independently guarantee these security controls. Implement and test them in the application.

---

Troubleshooting

The dashboard returns 404

Check that:

- The controller source is compiled into the running application.
- The route registration uses the path you are requesting.
- Another controller has not claimed the same path.
- The application was rebuilt and restarted.

Drogon cannot find a CSP view

Check that:

- The CSP file exists in the expected directory.
- The template has been compiled, registered, or loaded.
- The controller uses the correct view name.
- The build configuration includes the relevant view-generation step.

CSS or JavaScript returns 404

Check that:

- The requested URL is correct.
- The static route prefix matches the URL.
- The configured alias points to the correct directory.
- The file exists on disk.
- The application's working directory is correct when relative paths are used.

A generated controller is missing

Check that:

- The generator completed successfully.
- The output directory is correct.
- The file was not skipped because it already existed.
- The generated ".cc" file is included in the build target.

Include errors persist

Check that:

- The filename and extension are correct.
- The include path is valid relative to the source or configured include directories.
- CMake includes the required directories.
- The referenced file actually exists.
- The header contains the expected declarations.

A route is missing from the report

Check that:

- The correct source root was scanned.
- The route registration uses a recognized pattern.
- The route is not created dynamically at runtime.
- The route's registration is not hidden behind an unsupported macro or helper.

Regeneration did not update a file

Some generators skip existing files unless force mode is enabled. Inspect the script's behavior and use force mode only after backing up or reviewing the files that would be replaced.

---

Development Guidelines

Keep generated code separate from hand-written code

When possible, keep custom logic in separate files so regenerating the admin interface does not overwrite it.

Use version control

Commit the source generators and review generated-file diffs before committing them.

Test generated code

A successful Python run confirms only that the generator executed. It does not prove that the generated C++ compiles or that the views work.

Keep documentation synchronized

When modifying a generator, update this README with:

- Its exact purpose.
- Required inputs.
- Supported command-line options.
- Output paths and naming conventions.
- Overwrite behavior.
- Dependencies on other generators.
- Any changes to generated routes or templates.

Verify packaging

After using "pack.py", test extraction into a clean temporary directory and confirm the restored tree contains the expected files.

Review include repairs

After using "fix_includes.py", inspect the diff and build the project.

---

License and Attribution

Add the project's license, copyright notices, and third-party attribution requirements here before redistributing the toolkit.

Conclusion

The Drogon Admin Toolkit brings together model discovery, controller generation, dashboard generation, route inspection, single-file packaging, and include repair in one workflow.

Used carefully, these tools can reduce repetitive development work and make a Drogon application's administrative interface easier to maintain. Always review generated artifacts, verify build integration, test runtime behavior, and apply appropriate security controls before deployment.
