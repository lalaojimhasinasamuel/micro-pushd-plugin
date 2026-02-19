https://github.com/lalaojimhasinasamuel/micro-pushd-plugin/releases

# Micro Pushd Plugin: Shell-like Pushd, Popd, and Dirs for Micro

![Release](https://img.shields.io/github/v/release/lalaojimhasinasamuel/micro-pushd-plugin) ![Downloads](https://img.shields.io/github/downloads/lalaojimhasinasamuel/micro-pushd-plugin/total) ![License](https://img.shields.io/github/license/lalaojimhasinasamuel/micro-pushd-plugin)

A compact micro text editor plugin that brings shell-like directory navigation to Micro. It adds pushd, popd, and dirs commands so you can manage a directory stack while editing text. It is designed to be straightforward, reliable, and easy to customize. This plugin aims to feel native to Micro while offering a familiar navigation model for users who love shell workflows.

Introduction to the project’s goal is simple: help you move around directories without losing your place, keep your workflow smooth, and keep the focus on writing. The plugin uses a small memory-backed stack with a lightweight persistence option, so you can recover a session’s directory state if you quit Micro and come back later.

Key terms you’ll see in this document:
- pushd: push a directory onto the stack and switch to it.
- popd: pop the top directory from the stack and switch to it.
- dirs: display the current contents of the directory stack.
- stack: the in-memory list of directories you have pushed.
- persistence: optional saving of the stack to a file, so you can restore it later.

Overview and scope
- This plugin targets Micro users who want shell-style directory navigation inside the editor.
- It integrates with Micro’s command system and plugin API.
- It provides three primary commands: pushd, popd, and dirs.
- It includes optional configuration to enable persistence, customize the stack size, and set an initial working directory.
- It is designed to be cross-platform on Linux, macOS, and Windows (via a compatible executable or script, depending on the deployment method).

What this README covers
- How to install and initialize the plugin.
- How to use pushd, popd, and dirs in real-world editing sessions.
- How to configure and extend the plugin.
- How to contribute or fork the project.
- Common pitfalls and troubleshooting steps.
- A glance at the roadmap and future enhancements.

Important note about the releases page
- The link provided above points to the project’s releases page. For the latest assets, see the releases section. If you are looking to install or upgrade, you will typically download a file from that page and run it. The exact asset you download is described in the installation instructions below. For the latest assets and to verify checksums, visit the releases page: https://github.com/lalaojimhasinasamuel/micro-pushd-plugin/releases

Table of contents
- Why use a micro-pushd plugin?
- Features
- How it works under the hood
- Getting started
  - Prerequisites
  - Installation options
  - Quickstart
- Commands and behavior
  - pushd
  - popd
  - dirs
- Configuration and customization
- Examples and tutorials
- Advanced usage
- Development and contribution
- Testing and quality
- Troubleshooting
- Compatibility and limitations
- Security and safety
- Roadmap
- Acknowledgments and license

Why use a micro-pushd plugin?
Moving around directories is a core activity for many editors. In Micro, you often open files across different folders, perform quick edits, and then jump back to a prior location. The pushd, popd, and dirs commands provide a lightweight, shell-like experience that keeps your editing flow uninterrupted. Instead of repeatedly typing cd and remember paths, you maintain a small stack of directories you’ve visited recently. This approach reduces friction and lets you focus on your text work rather than on navigation.

What you gain with this plugin
- A simple, predictable directory stack that mirrors familiar shell behavior.
- A fast way to jump back and forth between work locations.
- A focused editing workflow with minimal setup.
- The option to persist the stack across sessions if desired.
- Easy customization to fit your preferred workflow and project layout.

Features
- pushd [DIR]: Push a directory onto the stack and switch the editor’s working directory to DIR. If DIR is omitted, push the current directory onto the stack.
- popd: Pop the top directory from the stack and switch to it. If the stack becomes empty, you remain in the current directory or fall back to a configured default.
- dirs: Display the current contents of the directory stack with the top of the stack shown first.
- Lightweight persistence: Optional save/load of the stack to a small file in your home directory.
- Configurable stack size: Cap the number of entries to avoid unbounded growth.
- Platform compatibility: Works with Micro on Linux, macOS, and Windows (where supported by the host environment).
- Safe defaults: Sensible defaults that avoid unexpected directory changes.

How it works under the hood
- The plugin registers three commands with Micro’s command system: pushd, popd, and dirs.
- It maintains a stack data structure in memory during a Micro session, with an optional persistence layer that writes to a JSON file in your user directory (for example, ~/.config/micro/pushd_stack.json).
- When you run pushd, the plugin resolves the target directory. If it is valid, the directory is appended to the stack and the editor’s working directory is updated.
- When you run popd, the plugin removes the top entry from the stack and switches to the next directory on the stack, if present.
- When you run dirs, the plugin prints a human-friendly list of the current stack, with the top entry highlighted for clarity.
- The configuration is lightweight by design. It avoids heavy state management and keeps changes fast and predictable.

Getting started
Prerequisites
- Micro editor installed and accessible in your PATH.
- Basic familiarity with Micro’s plugin system and command palette.
- A user home directory to store optional persistence data.

Installation options
- Option A: Automatic installer (recommended for most users)
  - The releases page contains an installer script that detects your platform and installs the plugin accordingly.
  - Download the file named install-micro-pushd-plugin.sh from the release assets.
  - Make the installer executable and run it:
    - chmod +x install-micro-pushd-plugin.sh
    - ./install-micro-pushd-plugin.sh
  - The installer sets up the plugin in Micro’s plugin directory and creates a default configuration file if needed.

  - Why use the installer? It handles platform quirks, puts the plugin in the right place, and helps you get started quickly without manual steps.

- Option B: Manual installation (for advanced users)
  - Clone the repository or download the plugin source from the main branch.
  - Copy the plugin file into Micro’s plugin directory, usually located at ~/.config/micro/plugins/ or a platform-specific location depending on your Micro version.
  - If the plugin ships with a Lua script or a binary, place the appropriate file in the plugin directory.
  - Ensure Micro loads the plugin on startup by following the editor’s plugin loading instructions.
  - Create or edit the configuration to enable persistence and set optional defaults.

- Option C: From source for development
  - Clone the repository.
  - Open Micro and run the development workflow as described in the developer guide below.
  - This route is ideal for contributors who want to modify behavior or add new commands.

Quickstart
- Open Micro.
- Run the following commands in Micro’s command line (you can usually access it via a command palette or a specific hotkey, depending on your Micro version):
  - pushd /path/to/project
  - pushd
  - dirs
  - popd
- Observe how the working directory changes and how the stack tracks your navigation.
- If you enabled persistence, you can restart Micro and use dirs again to see the saved state.

Notes on command behavior
- pushd without a path argument uses the current working directory as the entry.
- If you push a path that does not exist, the plugin will return an error message and will not modify the stack.
- popd when the stack is empty will leave you in the current directory and print a friendly message indicating the stack is empty.
- dirs prints the stack in a readable format. The top item is shown first, followed by the next items in order.

Commands and behavior
pushd
- Usage: pushd [DIR]
- If DIR is provided, the plugin checks that the directory exists. If it does, pushd adds DIR to the top of the stack and changes Micro’s working directory to DIR.
- If DIR is omitted, pushd uses the current directory. This allows you to push your current location onto the stack as a bookmark for later return.
- Error handling: If DIR does not exist, you receive a clear error message such as "No such file or directory: DIR" and the stack remains unchanged.

popd
- Usage: popd
- Pops the top directory from the stack and changes the working directory to the popped value.
- If the stack is empty, you stay in the current directory and a message explains that the stack is empty.
- The command is designed to be idempotent when the stack has one element left; after popd, the stack becomes empty, and you remain in the last valid directory or a configured fallback.

dirs
- Usage: dirs
- Renders the contents of the stack in a clean, readable format.
- The top of the stack (the most recently pushed directory) is shown prominently for quick recognition.
- This command is a quick snapshot that helps you reorient yourself after a long editing session.

Configuration and customization
Default behavior
- The plugin uses an in-memory stack by default from the moment Micro starts.
- It does not modify your normal workflow until you explicitly push or pop from the stack.
- If you enable persistence, the plugin writes the stack to a small file (for example, in your home directory) at appropriate times (on change, at exit, or on a periodic basis).

Persistence
- Persistence saves the stack to a JSON file in a user directory. This makes it possible to restore your session’s navigation state when you reopen Micro.
- You can disable persistence if you prefer a purely ephemeral session.
- The file path and format are designed to be human-readable and forgiving if you edit it by hand. A typical structure resembles:
  {
    "stack": ["/path/one", "/path/two"],
    "maxSize": 20
  }

Max stack size
- You can cap the stack size to avoid unbounded growth.
- If the stack exceeds the configured limit, the oldest entries are discarded as new ones are pushed.
- This keeps memory usage predictable and ensures you can focus on the current workflow.

Initial working directory
- You can set an initial working directory that Micro should switch to when the plugin initializes.
- This is useful when your editing session starts in a specific project directory or a designated working space.

Customizing commands
- The plugin exposes a few knobs in configuration to tailor its behavior:
  - maxSize: number
  - persist: true|false
  - startDir: path
  - ignoreMissingDirs: true|false
- You can edit the configuration file to adjust these values.
- For advanced users, there are hooks to extend or override command handlers if you want to integrate with other plugins or editing workflows.

Examples and tutorials
- Practical scenarios
  - Scenario A: You are working on multiple tasks in separate directories. You push each directory onto the stack as you switch tasks. When you are ready to return to the last task, you pop the stack and Micro switches back automatically.
  - Scenario B: You discover a directory you want to visit later. You push it, keep working, and later pop back to the previous project without losing your place.
  - Scenario C: You want to compare two versions of files across different folders. You push the directories you need, perform edits, jump back and forth, and keep the context intact.

- Step-by-step walkthrough
  - Start Micro in a project directory.
  - Push multiple directories:
    - pushd /home/user/projects/web
    - pushd /home/user/projects/mobile
  - View the stack:
    - dirs
  - Move back to the web project:
    - popd
  - Jump to a new location without losing track:
    - pushd /home/user/documents/specs
  - Reset to the previous working directory:
    - popd
  - Inspect outcomes and adjust your workflow as needed.

Advanced usage
- Combining with other plugins
  - You can combine pushd with file search or navigation plugins to automate workflows. For instance, you can push a directory, run a quick search, then pop back to continue editing the previous file.
- Scripting and automation
  - If you rely on micro’s scripting capabilities, you can write small wrappers that automate common navigation patterns, such as a sequence that pushes a series of directories and performs a set of edits before returning to the starting point.
- Cross-project navigation
  - The plugin can assist in moving between related projects. You can push the main project directory, then push sub-projects or feature branches, and jump back when needed.

Developer guide
- Architecture
  - The plugin is a lightweight Lua-based integration with Micro’s plugin API.
  - It consists of:
    - A simple in-memory stack data structure.
    - Command handlers for pushd, popd, and dirs.
    - Optional persistence logic to save and load the stack to a JSON file.
    - A configuration layer with sensible defaults.
- Extending the plugin
  - If you want to extend the plugin, you can add new commands that operate on the stack (for example, a command to clear the stack, or to rotate entries).
  - You can also add per-project behavior, such as a per-project initial directory or a custom persistence path.
- Testing
  - Unit tests can be written to simulate pushd, popd, and dirs without launching Micro.
  - End-to-end tests can simulate typical workflows by invoking the plugin’s API in a mocked Micro environment.
- Build and release
  - The release assets include an installer or binaries for various platforms.
  - Maintainers should update the installer when there are breaking changes or new features.
  - Always include a changelog entry with each release and keep a short note about migration steps if needed.

Contributing
- How to contribute
  - Fork the repository.
  - Create a feature branch for your changes.
  - Implement the feature and add tests.
  - Run the test suite to ensure no regressions.
  - Submit a pull request with a clear description of the changes and the motivation.
- Code style and conventions
  - Keep functions small and purposeful.
  - Use descriptive names for commands and internal helpers.
  - Document behavior clearly in code comments and in the README.
- Issues and discussions
  - Use the issue tracker to propose improvements, report bugs, or request features.
  - Engage in discussions with a constructive tone and present concrete scenarios or test cases.

Testing and quality
- Unit tests
  - Test the stack operations (push, pop, top, size) with normal and edge cases (empty stack, non-existent directories, exceeding max size).
- Integration tests
  - Validate how the plugin interacts with Micro’s API in a controlled environment.
  - Verify that persistence writes and reads the stack correctly.
- Linting and formatting
  - Use linting tools appropriate for the language and environment you are targeting.
  - Follow the project’s existing style guidelines to maintain consistency.

Troubleshooting
- Common issues and fixes
  - Micro cannot find the plugin: Verify that the plugin directory is in Micro’s plugin path and that the plugin file is readable.
  - Pushd reports a non-existent directory: Double-check the path for typos and confirm the directory exists on the system.
  - Stack not persisting across sessions: Verify persistence is enabled in the configuration and that the file path has appropriate write permissions.
  - Directory changes do not reflect: Ensure Micro is not running in a restricted mode and that the plugin loaded correctly.
- Logs and diagnostics
  - The plugin logs some basic diagnostics to the Micro console. If you encounter issues, review these messages and compare with the plugin’s documented expectations.

Compatibility and limitations
- Platform considerations
  - Linux and macOS are well supported. Windows support depends on the Micro environment and whether the release assets provide a compatible installer or script.
- Resource usage
  - The plugin uses a small in-memory stack and a JSON file for persistence. Memory and disk usage stay minimal.
- Edge cases
  - If a directory is moved or deleted while on the stack, the behavior is defined by the plugin’s error handling. The plugin warns the user and cleans up references if needed.

Security and safety
- File system access
  - The plugin reads and writes to user directories. It only switches the editor’s working directory as requested by pushd and popd.
- Data integrity
  - The persistence feature uses a JSON format with straightforward serialization. It validates data on load to prevent corruption.

Roadmap
- Planned enhancements
  - Per-project stacks that live inside a project’s workspace, isolated from other projects.
  - Richer history commands that allow jumping to a stack frame by index.
  - Graphical indicators for stack state within Micro’s UI.
  - Cross-editor compatibility with other text editors that support plugin ecosystems.
- Community contributions
  - The project welcomes contributors who can propose new features, share test cases, or provide translations for the user interface.

Acknowledgments
- The plugin owes its design to shell-style navigation concepts and to the Micro editor’s extensible plugin system.
- Thanks to users who provide feedback and report issues. Your input helps shape the project.

License
- This project is released under the MIT License. See the LICENSE file for details.

Releases and downloads
- For the latest assets and installation options, visit the releases page:
  https://github.com/lalaojimhasinasamuel/micro-pushd-plugin/releases
- From that page, download the installer file named install-micro-pushd-plugin.sh and run it to install the plugin automatically. If you prefer a manual route, you can copy the plugin files to Micro’s plugin directory and configure the settings yourself.

Usage quick reference
- pushd [DIR]
  - Push DIR onto the stack and switch to DIR. If DIR is omitted, push the current directory.
- popd
  - Pop the top directory from the stack and switch to it. If the stack is empty, stay in the current directory.
- dirs
  - Show the current stack with the top entry highlighted.

Appendix: Frequently asked questions
- Can I use this plugin with Micro on Windows?
  - Yes, provided you have a compatible Micro setup and an installation package designed for Windows. If a Windows installer is not available, you can install manually using the repository’s instructions.
- Does the plugin support per-project configurations?
  - Yes. You can enable per-project persistence and set a project-specific start directory.
- Is the stack shared across Micro sessions?
  - It can be, if you enable persistence. Without persistence, the stack exists only for the duration of the Micro session.

Appendix: Sample configuration (JSON)
- Example of a simple configuration file:
  {
    "persist": true,
    "startDir": "/home/user",
    "maxSize": 20,
    "ignoreMissingDirs": true
  }

Appendix: Example workflows
- Workflow 1: Quick switch between two project folders
  - pushd ~/projects/project-a
  - do edits
  - popd
  - pushd ~/projects/project-b
  - do edits
  - popd
  - dirs
- Workflow 2: Keep a working directory anchor while exploring subfolders
  - pushd /workspace/projects
  - pushd /workspace/projects/project-a/submodule
  - pushd
  - popd
  - popd

Appendix: Troubleshooting tips for installer users
- If the installer fails to set up the plugin automatically, check the plugin path in Micro’s settings and verify you have write permissions in the plugin directory.
- If you see a mismatch between the displayed stack and the actual directory, clear the local persistence file and start a fresh session to reinitialize the stack.

Appendix: Credits for assets
- The project borrows ideas from classic shell navigation commands and adapts them for a lightweight text editor plugin.
- Visual and badge assets are sourced from open resources that provide shields and status indicators for GitHub projects.

Final notes
- This plugin aims to be a practical tool for daily editing. It emphasizes speed, reliability, and minimalism. If you have feature requests or bug reports, open an issue in the repository so maintainers can review and respond. The project welcomes thoughtful contributions and strives to stay aligned with community needs without compromising simplicity.