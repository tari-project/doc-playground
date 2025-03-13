# Installing your IDE
An IDE (Integrated Development Environment) is a software application that provides various tools and functions for editing code and managing development projects. There are many different IDEs available for use, but we will be using VS Code for the guide.

Rather than explain the exact process for installing VS Code, it is best to follow the official instructions for installing VS Code, as the installation steps may change over time and differ for different environments.

Use one of the following links below, depending on your operating system, to install VS Code:

- Linux: [https://code.visualstudio.com/docs/setup/linux](https://code.visualstudio.com/docs/setup/linux)
- MacOS: [https://code.visualstudio.com/docs/setup/mac](https://code.visualstudio.com/docs/setup/mac)
- Windows: [https://code.visualstudio.com/docs/setup/windows](https://code.visualstudio.com/docs/setup/windows)

Follow the instructions, then launch VS Code to confirm it’s working as intended. Next, we’ll install the prerequisites for working on Tari projects.

## Installing Development Prerequisites
While an IDE is generally the only thing you would require if you were programming your own small projects, modern development environments rely heavily on several applications and services that improves the overall process of development:

Below we’ve listed several items which will be useful to have when working with Tari projects.

- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Nvm (for Node.js and npm).](https://github.com/nvm-sh/nvm)
- MacOS: [Homebrew.](https://brew.sh/)
- [Rust language](https://www.rust-lang.org/tools/install)
- [Protocol Buffers](https://grpc.io/docs/protoc-installation/)
- [Cmake](https://cmake.org/download/) and make
- [OpenSSL](https://docs.openssl.org/3.2/man7/ossl-guide-introduction/#getting-and-installing-openssl)
- [Perl][https://learn.perl.org/installing/]
- LLVM
- Tor (Optional)

It’s best to follow the official instructions for installing these items. We’ve linked to each one’s current installation page as a quick reference. Also note that you might already have some of these prerequisites installed. You can also review the Tari home page for additional requirements

VS Code Layout

When you first open **Visual Studio Code (VS Code)**, you’re greeted with a clean and simple interface designed to help you work with code. Here’s a breakdown of the main areas you’ll see:

1. **Top Menu Bar**:  
   At the very top, you'll find the menu bar with options like "File," "Edit," "View," and others. These options let you perform actions like opening files, saving your work, or adjusting the settings.

2. **Activity Bar (Left Side)**:  
   On the left side of the screen, you’ll see a vertical bar that gives you quick access to important tools:
   - **Explorer**: This is where you can browse and open files or folders on your computer.
   - **Search**: You can search through your files for specific text.
   - **Source Control**: This is used for tracking changes in your code (if you're using something like Git).
   - **Extensions**: This lets you install plugins to add extra features to VS Code.

3. **Side Panel (Left Sidebar)**:  
   When you select an option from the Activity Bar, this panel shows the details. For example, if you select "Explorer," it shows a list of your files. If you choose "Source Control," it shows changes in your code that need attention.

4. **Editor Area (Middle)**:  
   The large area in the middle of the screen is where you’ll do most of your work. This is where your files open up, and you can write, edit, and view your code. You can open multiple files here and switch between them using tabs at the top of the editor.

5. **Status Bar (Bottom)**:  
   At the very bottom of the screen, you'll find the **Status Bar**. It shows helpful information like which file you're working on, if there are any errors, and what programming language you're using. It's also where you can see any notifications.

6. **Terminal (Bottom Panel)**:  
   At the bottom, there’s a **Terminal** that you can open to run commands directly from VS Code. The Terminal lets you type in commands that you might need to work with your project, like running a program or installing software. You can open it by clicking "Terminal" in the top menu, or by pressing `Ctrl + ` (the backtick key, usually under the `Esc` key). You can open multiple terminals and switch between them if needed.
