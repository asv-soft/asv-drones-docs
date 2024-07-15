### Building from source code

To ensure a smooth development experience, follow the steps below to set up your development environment:

#### 3.1 Prerequisites:
- **Operating System:** This project is compatible with Windows, macOS, and Linux.
- **IDE (Integrated Development Environment):** We recommend using [Visual Studio](https://visualstudio.microsoft.com/) or [JetBrains Rider](https://www.jetbrains.com/rider/) as your IDE for C# development. Make sure to install the necessary extensions and plugins for a better development experience.

    If you are using [JetBrains Rider](https://www.jetbrains.com/rider/) you should install [AvaloniaRider](https://docs.avaloniaui.net/docs/reference/jetbrains-rider-ide/jetbrains-rider-setup#install-the-avalonia-plugin) plugin to be able working with axaml files using a preview. 

    If you are using [Visual Studio](https://visualstudio.microsoft.com/) you should install [Avalonia for Visual Studio](https://docs.avaloniaui.net/docs/get-started/set-up-an-editor#visual-studio) plugin to be able working with axaml files using a preview.

#### 3.2 .NET Installation:
- This project is built using [.NET 8.0](https://dotnet.microsoft.com/download/dotnet/8.0), the latests version of the .NET platform. We recommend installing .NET 8.0 by following the instructions provided on the official [.NET website](https://dotnet.microsoft.com/download/dotnet/8.0).

   ```bash
   # Check your current .NET version
   dotnet --version
   ```

#### 3.3 Version Control:
- If you haven't already, install a version control system such as [Git](https://git-scm.com/) to track changes and collaborate with other developers.

#### 3.4 Clone the Repository:
- Clone the project repository to your local machine using the following command:

   ```bash
   git clone https://github.com/asv-soft/asv-drones.git
   ```

#### 3.5 Restore Dependencies:
- Navigate to the platform project directory and restore the required dependencies. There is 3 possible platform directories to build and debug our app: __Asv.Drones.Gui.Desktop__, __Asv.Drones.Gui.Android__, __Asv.Drones.Gui.iOS__.
For example we will use __Asv.Drones.Gui.Desktop__ platform, so you have to execute the following command:

   ```bash
   cd asv-drones/src/Asv.Drones.Gui.Desktop
   dotnet workload restore
   dotnet workload repair
   ```

#### 3.6 Build and Run:
- After restore you have to build the project to ensure that everything is set up correctly, and if it's not - try to restore workloads again:

   ```bash
   dotnet build
   ```

- Run the project:

   ```bash
   dotnet run
   ```

Congratulations! Your development environment is now set up, and you are ready to start contributing to the project. If you encounter any issues during the setup process, refer to the project's documentation or reach out to the development team for assistance.

#### Building for Android

To build applications for Android, additional setup is required for JDK and Android SDK installation. Follow the instructions below based on your operating system.

- Navigate to the platform project directory and restore the required dependencies:

   ```bash
   cd asv-drones/src/Asv.Drones.Gui.Android
   dotnet workload restore
   dotnet workload repair
   ```

##### Windows

1. Install the .NET MAUI Check tool to verify your environment is ready for .NET MAUI development:
   ```
   dotnet tool install -g Redth.Net.Maui.Check
   maui-check
   ```

2. For Android SDK managing we recommend to install Android Studio.

3. Using Android Studio's SDK Manager, download Android 13.0 (Tiramisu) and API level 33. It's highly recommended to create an Android Virtual Device (AVD) with these settings, preferably with tablet configurations for better testing experience.

4. Build the project for Android:
   ```
   dotnet build -t:Run -f net7.0-android /p:AndroidSdkDirectory=${AndroidSdkPath}
   ```

- The `${AndroidSdkPath}` should be replaced with the actual path to your Android SDK installation.

##### Linux

1. Install Android Studio to manage Android SDKs:
   ```
   sudo snap install android-studio --classic
   ```
2. Install OpenJDK 11:
   ```
   sudo apt install openjdk-11-jdk
   ```
3. Using Android Studio's SDK Manager, download Android 13.0 (Tiramisu) and API level 33. It's highly recommended to create an Android Virtual Device (AVD) with these settings, preferably with tablet configurations for better testing experience.
4. Build the project for Android:
   ```
   dotnet build -f net7.0-android /p:AndroidSdkDirectory=${AndroidSdkPath}
   ```
- The `${AndroidSdkPath}` should be replaced with the actual path to your Android SDK installation.

##### macOS

1. Install Android Studio:
   ```
   brew install --cask android-studio
   ```
2. Install JDK through Homebrew or any preferred method:
   ```
   brew install openjdk
   ```
3. Using Android Studio's SDK Manager, download Android 13.0 (Tiramisu) and API level 33. It's highly recommended to create an Android Virtual Device (AVD) with these settings, preferably with tablet configurations for better testing experience.
4. Build the project for Android, specifying the Android SDK directory:
   ```
   dotnet build -f net7.0-android /p:AndroidSdkDirectory=${AndroidSdkPath}
   ```
- The `${AndroidSdkPath}` should be replaced with the actual path to your Android SDK installation.

#### Additional Notes

- If you want to run application after build you should start your previously created AVD and wait until it's startup processes are complete. Then you have to execute following command:
    ```
    dotnet run -f net7.0-android /p:AndroidSdkDirectory=${AndroidSdkPath}
    ```
- The `${AndroidSdkPath}` should be replaced with the actual path to your Android SDK installation.


##### Windows environment setup example
![](images/windows-download-dotnet-sdk.png)
![](images/windows-install-dotnet-sdk-1.png)
![](images/windows-install-dotnet-sdk-2.png)
![](images/windows-install-dotnet-sdk-3.png)
![](images/windows-git-clone-asv-drones-repo.png)
![](images/windows-asv-drones-repo-platform-select.png)
![](images/windows-asv-drones-desktop-workload-restore.png)
![](images/windows-asv-drones-desktop-workload-repair.png)
![](images/windows-asv-drones-desktop-build.png)
![](images/windows-asv-drones-desktop-run.png)
![](images/windows-asv-drones-desktop.png)
##### Ubuntu environment setup example

![](images/ubuntu-apt-get-dotnet-sdk-install.jpg)
![](images/ubuntu-apt-get-dotnet-sdk-install-complete.jpg)
![](images/ubuntu-apt-get-dotnet-sdk-check-version.jpg)
![](images/ubuntu-clone-asv-drones-repo.jpg)
![](images/ubuntu-select-asv-drones-repo-platform-to-build.jpg)
![](images/ubuntu-asv-drones-repo-desktop-workload-restore.jpg)
![](images/ubuntu-asv-drones-repo-desktop-build.jpg)
![](images/ubuntu-asv-drones-repo-desktop.jpg)

##### macOS environment setup example

![](images/macos-dotnet-sdk-install.jpg)
![](images/macos-dotnet-sdk-install-complete.jpg)
![](images/macos-asv-drones-repo.jpg)
![](images/macos-asv-drones-repo-complete.jpg)
![](images/macos-asv-drones-repo-desktop-workload-restore.jpg)
![](images/macos-asv-drones-repo-desktop-workload-restore-complete.jpg)
![](images/macos-asv-drones-repo-desktop-build.jpg)
![](images/macos-asv-drones-repo-desktop-build-complete.jpg)
![](images/macos-asv-drones-repo-desktop-run.jpg)
![](images/macos-asv-drones-repo-desktop.jpg)