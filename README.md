# dotnet-oci-explore
Explore the process to work with .NET on atomic desktops

In particular should one:
- install using `brew`?
- construct a `distrobox` to use with a `devcontainer`? - e.g., [klmcwhirter/oci-shared-images](https://github.com/klmcwhirter/oci-shared-images)

The approach selected should be general in nature - i.e., works with CLI, VS Code or Jetbrains Rider IDE.


## Disclaimer
I admittedly did not spend a lot of time on Rider because I just do not like it. But I did install the flatpak and did try to capture some interesting tips as I encountered them.


## Brew
Installing with `brew` on linux is a simple matter of executing `brew install dotnet` and following the post-install suggestions.

### brew post-install setup
Executing `brew info dotnet` shows the following:

```
brew info dotnet
==> dotnet: stable 9.0.4 (bottled), HEAD
.NET Core
https://dotnet.microsoft.com/
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/d/dotnet.rb
License: MIT
==> Dependencies
Build: cmake ✘, pkgconf ✘, rapidjson ✘, python ✘
Required: brotli ✘, icu4c@77 ✔, openssl@3 ✔, libunwind ✘, lttng-ust ✘, krb5 ✘, zlib ✔
==> Options
--HEAD
	Install HEAD version
==> Caveats
For other software to find dotnet you may need to set:
  export DOTNET_ROOT="/home/linuxbrew/.linuxbrew/opt/dotnet/libexec"
```

Of import when using brew is:

**`export DOTNET_ROOT="/home/linuxbrew/.linuxbrew/opt/dotnet/libexec"`**

When using distrobox (or installing with dnf in general) this is done for you.

## Distrobox / Devcontainer
As a starting point, create fedora41-dotnet-dx image per [klmcwhirter/oci-shared-images](https://github.com/klmcwhirter/oci-shared-images).

> Note that you can use tags for multiple versions: `fedora41-dotnet-dx:8` or `fedora41-dotnet-dx:9`

Then create devcontainer from there. _See [devcontainer.json](./.devcontainer/devcontainer.json) here_

## Docker
Finally another option is to just use `docker` directly. This approach is most meaningful when wanting to run code from a remote git repo
without cloning it locally.

## Suggestions

|Goal|Comments|
| --- | --- |
|CLI|Just use `brew` to install; it will feel more natural|
|CLI with multiple tool chain versions|Use separate distroboxen for each version; you'll be glad you did|
|IDE|Use VS Code with devcontainers|
|IDE with multiple tool chain versions|Use VS Code with devcontainers; swap base image tag to switch tool versions (and in .csproj of course)|
|Build and/or execute remote code without cloning repo|Use docker directly|

## Results

> Note that I mostly work in a combination of VS Code, VIM and emacs and so I did not spend a lot of time with Rider. But I did install the flatpak and did try to capture some interesting tips as I encountered them.

To test the approach matrix below I used the `dotnetapp` sample (original linked to in References below and attribution in [LICENSE](./LICENSE)).

|Approach|IDE|Comments|
| --- | --- | --- |
|brew|CLI / VIM|ran `dotnet run --project dotnetapp` just fine|
|brew|Jetbrains Rider|See first_run.txt from jetbrains-flatpak-rider to enable execution from host (e.g., `brew`). It opens automatically on the first start of Rider.|
|brew|VS Code|works just fine - see below|
|devcontainer|CLI / VIM|didn't test - but see https://cadu.dev/running-neovim-on-devcontainers/|
|devcontainer|Jetbrains Rider|didn't test - but see https://www.jetbrains.com/help/rider/Prerequisites_for_dev_containers.html|
|devcontainer|VS Code|custom devcontainer.json - identified Toolbx Container Image environment; hostname is devcontainer short hash|
|distrobox|CLI / VIM|worked as expected|
|distrobox|Jetbrains Rider|didn't test - but it should be possible to install Rider in the distrobox and execute it from there|
|distrobox|VS Code|worked as expected|
|docker|**|No issues, output as expected including hostname|

### Brew / CLI and Brew / VS Code
```
$ dotnet run --project dotnetapp
         42                                                    
         42              ,d                             ,d     
         42              42                             42     
 ,adPPYb,42  ,adPPYba, MM42MMM 8b,dPPYba,   ,adPPYba, MM42MMM  
a8"    `Y42 a8"     "8a  42    42P'   `"8a a8P_____42   42     
8b       42 8b       d8  42    42       42 8PP!!!!!!!   42     
"8a,   ,d42 "8a,   ,a8"  42,   42       42 "8b,   ,aa   42,    
 `"8bbdP"Y8  `"YbbdP"'   "Y428 42       42  `"Ybbd8"'   "Y428  

OSArchitecture: X64
OSDescription: Bluefin (Version: 41.20250420 / FROM Fedora Silverblue 41)
FrameworkDescription: .NET 9.0.4

UserName: klmcw
HostName : nitro5

ProcessorCount: 12
TotalAvailableMemoryBytes: 16444526592 (15.315 GiB)
```

### Devcontainer / VS Code and Distrobox / CLI and Distrobox / VS Code
```
$ dotnet run --project dotnetapp
         42                                                    
         42              ,d                             ,d     
         42              42                             42     
 ,adPPYb,42  ,adPPYba, MM42MMM 8b,dPPYba,   ,adPPYba, MM42MMM  
a8"    `Y42 a8"     "8a  42    42P'   `"8a a8P_____42   42     
8b       42 8b       d8  42    42       42 8PP!!!!!!!   42     
"8a,   ,d42 "8a,   ,a8"  42,   42       42 "8b,   ,aa   42,    
 `"8bbdP"Y8  `"YbbdP"'   "Y428 42       42  `"Ybbd8"'   "Y428  

OSArchitecture: X64
OSDescription: Fedora Linux 41 (Toolbx Container Image)
FrameworkDescription: .NET 9.0.3

UserName: klmcw
HostName : nitro5

ProcessorCount: 12
TotalAvailableMemoryBytes: 16444526592 (15.315 GiB)
```

### Docker from github.com and local
```
$ docker build --pull -t dotnetapp 'https://github.com/dotnet/dotnet-docker.git#:samples/dotnetapp'
-or-
$ docker build -t dotnetapp dotnetapp

$ docker run --rm dotnetapp 
         42                                                    
         42              ,d                             ,d     
         42              42                             42     
 ,adPPYb,42  ,adPPYba, MM42MMM 8b,dPPYba,   ,adPPYba, MM42MMM  
a8"    `Y42 a8"     "8a  42    42P'   `"8a a8P_____42   42     
8b       42 8b       d8  42    42       42 8PP!!!!!!!   42     
"8a,   ,d42 "8a,   ,a8"  42,   42       42 "8b,   ,aa   42,    
 `"8bbdP"Y8  `"YbbdP"'   "Y428 42       42  `"Ybbd8"'   "Y428  

OSArchitecture: X64
OSDescription: Debian GNU/Linux 12 (bookworm)
FrameworkDescription: .NET 9.0.4

UserName: app
HostName : 57fddaf455f6

ProcessorCount: 12
TotalAvailableMemoryBytes: 16444526592 (15.32 GiB)
```

## References
- [klmcwhirter/oci-shared-images](https://github.com/klmcwhirter/oci-shared-images)
- https://github.com/dotnet/dotnet-docker/tree/main/samples/dotnetapp
- https://github.com/dotnet/dotnet-docker/blob/main/documentation/image-variants.md
- https://hub.docker.com/r/microsoft/dotnet-sdk
- https://cadu.dev/running-neovim-on-devcontainers/
- https://www.jetbrains.com/help/rider/Dev-Containers-starting-page.html
