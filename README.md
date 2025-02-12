# OpenArena Engine [![Build Status](https://travis-ci.org/OpenArena/engine.png?branch=master)](https://travis-ci.org/OpenArena/engine) #

This project is a fork of ioquake3 with OpenArena specific changes to the
client and server.

## Building ##

This a standard ioquake3 build which they describe [here](http://wiki.ioquake3.org/Building_ioquake3)

It's not an autotools based build.  If you don't have the dependencies, it
will break in the middle of the build.

If you are on Ubuntu or Debian, the easiest way to compile this is to install
the build dependencies for the "ioquake3" package.

```sh
$ sudo aptitude build-dep ioquake3
$ sudo apt-get install libsdl1.2-dev libxmp-dev
$ git clone https://github.com/OpenArena/engine.git
$ cd engine
$ make
```

You may want to change a few things in Makefile.local.  Other than installing
the build dependencies, you shouldn't need to do anything else.  By default it
builds a modular renderer so you need the binary + *.so or *.dll files in the
same directory to run it.

## Development ##

```sh
# Get this project or sign up on github and fork it
$ git clone https://github.com/OpenArena/engine.git
$ cd engine

# Create a reference to the upstream project
$ git remote add upstream https://github.com/ioquake/ioq3.git

# View changes in this project compared to ioquake3
$ git fetch upstream
$ git diff upstream/master
```

## Switching renderers ##

Recent ioquake3 versions allow for a modular renderer.  This allows you to
select the renderer at runtime rather than compiling in one into the binary.

This feature is enabled by default.  If you wish to disable it, uncomment
USE_RENDERER_DLOPEN=0 in Makefile.local.  With ioquake3, this embeds the
opengl1 renderer.  In OpenArena, it embeds the openarena1 renderer.

When you start OpenArena, you can pass the name of the dynamic library to
load.  ioquake3 assumes a naming convention renderer_*_.

Example:

```sh
# Enable the default OpenArena renderer with GLSL, bloom support and more.
# This is based on the renderergl1 code.
$ ./openarena.x86_64 +set cl_renderer openarena1

# Enable the default ioquake3 renderer (renderergl1).
# If you are having trouble with OA's renderer, this is a safe alternative.
$ ./openarena.x86_64 +set cl_renderer opengl1

# Enable renderergl2 which is now in upstream
# It doesn't work with OpenArena yet
$ ./openarena.x86_64 +set cl_renderer opengl2
```

## TODO ##

* Wait for ioquake3 to add/reject vorbis so we can fix Windows builds
    - Cross compiling on Windows works in experimental/latest-libraries branch
* Wait for ioquake3 to add/reject latest curl
    - Required to fix build problems on Windows for some users
    - Also in the experimental/latest-libraries branch
* Verify that allowing say/say_team to bypass Cmd_Args_Sanitize is safe.
* Try to avoid changing qcommon area to support GLSL.  Canete's renderergl2
  didn't need this change so this renderer shouldn't either.
* Build in FreeBSD and Mac OS X
* Remove compiler warnings.  I kept them in for now so the code would be
  as close as possible to 0.8.8.
* Potential GLSL debugging fix that was made available after 0.8.8 release
    - Need to find a link to this.  Is this real?
* Fix Ogg Vorbis music looping hitching bug.
* Software rendering module based on TinyGL, optimized for performance.
* Direct3D7 rendering module.  7 particularly, for compatibility with more legacy graphics chipsets and underperforming onboard video chipsets without a good OpenGL driver. 
* Restore Win9X support (Regressions are in PSAPI and the TCP stack)
* Restore the use of the WGL_3DFX_gamma_control extension

## Status ##

* Initial testing on Debian and Windows
* Need help testing on other platforms
* Need help testing the SDL2 branch since ioquake3 is moving to SDL2
    - sdl2 branch is considered finished in ioquake3 so it is a good base
    - See experimental/latest-libraries-sdl2 branch in this repository to
      test with OpenArena.

## Changes from 0.8.8 release ##

* Sync with upstream so openarena.i386 uses the OA 0.8.8 renderer,
  openarena_opengl2.i386 uses Canete's opengl2 and openarena_opengl1.i386
  uses the default renderer.  (plus minor updates).
* OA's renderer is now in renderer_oa and the base ioquake3 renderer is left
  untouched
* This does not remove the game or cgame files.  They are never referenced or
  built.  This makes it easier to keep in sync with upstream.  It would also
  be possible to integrate the OA engine and OA game code at some point in the
  future.
* Makefile has fewer changes since the recent upstream Makefile makes it easier
  to support standalone games
* cl_yawspeed and cl_pitchspeed are CVAR_CHEAT instead of removing the
  variables and using a constant.
* r_aviMotionJpegQuality was left untouched
* Enables STANDALONE so the CD key and authorize server related changes are
  no longer needed.
* Enables LEGACY_PROTOCOL and sets the version to 71.
* This uses a different GAMENAME_FOR_MASTER than 0.8.8.  It also uses the new
  HEARTBEAT_FOR_MASTER name since the code says to leave it unless you have a
  good reason.
* Any trivial whitespace changes were left out


## renderer_oa changes ##

* Widescreen horizontal expansion support
* Paletted texturing support, for the few older cards that support the paletted texture extensions (3dfx)
* Many changes to the flares code - sun flares, support for custom flare shaders, a lower quality flare option (huge framerate jump on oa_pvomit), additional lens reflection effects, configurable lens reflection types for dynamic lights, sun or map lights
* Brightness by blended quad, allowing overbrights to work in a window without the need for a fragment shader
* More postprocessing effects, including a multipass shader effect for simulating the looks of a popular 1996 3d card
* Slowness feature to simulate the rough speed of a typical 6th generation computer setup by default (common 1998 computers), potentially useful for content producers looking to optimize their stuff


## client changes ##

* Module player for music as an alternative to streams (.mod, .it, .xm and .s3m formats). Uses the libxmp library.


# ioquake3 README #

                   ,---------------------------------------.
                   |   _                     _       ____  |
                   |  (_)___  __ _ _  _ __ _| |_____|__ /  |
                   |  | / _ \/ _` | || / _` | / / -_)|_ \  |
                   |  |_\___/\__, |\_,_\__,_|_\_\___|___/  |
                   |            |_|                        |
                   |                                       |
                   `---------- http://ioquake3.org --------'

The intent of this project is to provide a baseline Quake 3 which may be used
for further development and baseq3 fun. 
Some of the major features currently implemented are:

  * SDL backend
  * OpenAL sound API support (multiple speaker support and better sound
    quality)
  * Full x86_64 support on Linux
  * VoIP support, both in-game and external support through Mumble.
  * MinGW compilation support on Windows and cross compilation support on Linux
  * AVI video capture of demos
  * Much improved console autocompletion
  * Persistent console history
  * Colorized terminal output
  * Optional Ogg Vorbis support
  * Much improved QVM tools
  * Support for various esoteric operating systems
  * cl_guid support
  * HTTP/FTP download redirection (using cURL)
  * Multiuser support on Windows systems (user specific game data
    is stored in "%APPDATA%\Quake3")
  * PNG support
  * Many, many bug fixes

The map editor and associated compiling tools are not included. We suggest you
use a modern copy from http://icculus.org/gtkradiant/.

The original id software readme that accompanied the Q3 source release has been
renamed to id-readme.txt so as to prevent confusion. Please refer to the
web-site for updated status.


# Compilation and installation

For *nix
  1. Change to the directory containing this readme.
  2. Run 'make'.

For Windows,
  1. Please refer to the excellent instructions here: 
     http://wiki.ioquake3.org/Building_ioquake3

For Mac OS X, building a Universal Binary
  1. Install MacOSX SDK packages from XCode.  For maximum compatibility,
     install MacOSX10.4u.sdk and MacOSX10.3.9.sdk, and MacOSX10.2.8.sdk.
  2. Change to the directory containing this README file.
  3. Run './make-macosx-ub.sh'
  4. Copy the resulting ioquake3.app in /build/release-darwin-ub to your
     /Applications/ioquake3 folder.

Installation, for *nix
  1. Set the COPYDIR variable in the shell to be where you installed Quake 3
     to. By default it will be /usr/local/games/quake3 if you haven't set it.
     This is the path as used by the original Linux Q3 installer and subsequent
     point releases.
  2. Run 'make copyfiles'.

It is also possible to cross compile for Windows under *nix using MinGW. Your
distribution may have mingw32 packages available. On debian/Ubuntu, you need to
install 'mingw-w64'. Thereafter cross compiling is simply a case running
'PLATFORM=mingw32 ARCH=x86 make' in place of 'make'. ARCH may also be set to
x86_64.

The following variables may be set, either on the command line or in
Makefile.local:

```
  CFLAGS             - use this for custom CFLAGS
  V                  - set to show cc command line when building
  DEFAULT_BASEDIR    - extra path to search for baseq3 and such
  BUILD_SERVER       - build the 'ioq3ded' server binary
  BUILD_CLIENT       - build the 'ioquake3' client binary
  BUILD_BASEGAME     - build the 'baseq3' binaries
  BUILD_MISSIONPACK  - build the 'missionpack' binaries
  BUILD_GAME_SO      - build the game shared libraries
  BUILD_GAME_QVM     - build the game qvms
  BUILD_STANDALONE   - build binaries suited for stand-alone games
  SERVERBIN          - rename 'ioq3ded' server binary
  CLIENTBIN          - rename 'ioquake3' client binary
  BASEGAME           - rename 'baseq3'
  BASEGAME_CFLAGS    - custom CFLAGS for basegame
  MISSIONPACK        - rename 'missionpack'
  MISSIONPACK_CFLAGS - custom CFLAGS for missionpack (default '-DMISSIONPACK')
  USE_OPENAL         - use OpenAL where available
  USE_OPENAL_DLOPEN  - link with OpenAL at runtime
  USE_CURL           - use libcurl for http/ftp download support
  USE_CURL_DLOPEN    - link with libcurl at runtime
  USE_CODEC_VORBIS   - enable Ogg Vorbis support
  USE_CODEC_OPUS     - enable Ogg Opus support
  USE_CODEC_XMP      - enable libxmp support for Module player
  USE_MUMBLE         - enable Mumble support
  USE_VOIP           - enable built-in VoIP support
  USE_INTERNAL_SPEEX - build internal speex library instead of dynamically
                       linking against system libspeex
  USE_FREETYPE       - enable FreeType support for rendering fonts
  USE_INTERNAL_ZLIB  - build and link against internal zlib
  USE_INTERNAL_JPEG  - build and link against internal JPEG library
  USE_INTERNAL_OGG   - build and link against internal ogg library
  USE_INTERNAL_OPUS  - build and link against internal opus/opusfile libraries
  USE_LOCAL_HEADERS  - use headers local to ioq3 instead of system ones
  DEBUG_CFLAGS       - C compiler flags to use for building debug version
  COPYDIR            - the target installation directory
  TEMPDIR            - specify user defined directory for temp files
```

The defaults for these variables differ depending on the target platform.


# Console

## New cvars

```
  cl_autoRecordDemo                 - record a new demo on each map change
  cl_aviFrameRate                   - the framerate to use when capturing video
  cl_aviMotionJpeg                  - use the mjpeg codec when capturing video
  cl_guidServerUniq                 - makes cl_guid unique for each server
  cl_cURLLib                        - filename of cURL library to load
  cl_consoleKeys                    - space delimited list of key names or
                                      characters that toggle the console
  cl_mouseAccelStyle                - Set to 1 for QuakeLive mouse acceleration
                                      behaviour, 0 for standard q3
  cl_mouseAccelOffset               - Tuning the acceleration curve, see below

  in_joystickUseAnalog              - Do not translate joystick axis events
                                      to keyboard commands

  j_forward                         - Joystick analogue to m_forward,
                                      for forward movement speed/direction.
  j_side                            - Joystick analogue to m_side,
                                      for side movement speed/direction.
  j_up                              - Joystick up movement speed/direction.
  j_pitch                           - Joystick analogue to m_pitch,
                                      for pitch rotation speed/direction.
  j_yaw                             - Joystick analogue to m_yaw,
                                      for yaw rotation speed/direction.
  j_forward_axis                    - Selects which joystick axis
                                      controls forward/back.
  j_side_axis                       - Selects which joystick axis
                                      controls left/right.
  j_up_axis                         - Selects which joystick axis
                                      controls up/down.
  j_pitch_axis                      - Selects which joystick axis
                                      controls pitch.
  j_yaw_axis                        - Selects which joystick axis
                                      controls yaw.

  s_useOpenAL                       - use the OpenAL sound backend if available
  s_alPrecache                      - cache OpenAL sounds before use
  s_alGain                          - the value of AL_GAIN for each source
  s_alSources                       - the total number of sources (memory) to
                                      allocate
  s_alDopplerFactor                 - the value passed to alDopplerFactor
  s_alDopplerSpeed                  - the value passed to alDopplerVelocity
  s_alMinDistance                   - the value of AL_REFERENCE_DISTANCE for
                                      each source
  s_alMaxDistance                   - the maximum distance before sounds start
                                      to become inaudible.
  s_alRolloff                       - the value of AL_ROLLOFF_FACTOR for each
                                      source
  s_alGraceDistance                 - after having passed MaxDistance, length
                                      until sounds are completely inaudible
  s_alDriver                        - which OpenAL library to use
  s_alDevice                        - which OpenAL device to use
  s_alAvailableDevices              - list of available OpenAL devices
  s_alInputDevice                   - which OpenAL input device to use
  s_alAvailableInputDevices         - list of available OpenAL input devices
  s_sdlBits                         - SDL bit resolution
  s_sdlSpeed                        - SDL sample rate
  s_sdlChannels                     - SDL number of channels
  s_sdlDevSamps                     - SDL DMA buffer size override
  s_sdlMixSamps                     - SDL mix buffer size override
  s_backend                         - read only, indicates the current sound
                                      backend
  s_muteWhenMinimized               - mute sound when minimized
  s_muteWhenUnfocused               - mute sound when window is unfocused
  sv_dlRate                         - bandwidth allotted to PK3 file downloads
                                      via UDP, in kbyte/s

  com_ansiColor                     - enable use of ANSI escape codes in the tty
  com_altivec                       - enable use of altivec on PowerPC systems
  com_standalone (read only)        - If set to 1, quake3 is running in
                                      standalone mode
  com_basegame                      - Use a different base than baseq3. If no
                                      original Quake3 or TeamArena pak files
                                      are found, this will enable running in
                                      standalone mode
  com_homepath                      - Specify name that is to be appended to the
                                      home path
  com_legacyprotocol                - Specify protocol version number for
                                      legacy Quake3 1.32c protocol, see
                                      "Network protocols" section below
                                      (startup only)
  com_maxfpsUnfocused               - Maximum frames per second when unfocused
  com_maxfpsMinimized               - Maximum frames per second when minimized
  com_busyWait                      - Will use a busy loop to wait for rendering
                                      next frame when set to non-zero value
  com_pipefile                      - Specify filename to create a named pipe 
                                      through which other processes can control
                                      the server while it is running. 
                                      Nonfunctional on Windows.
  com_gamename                      - Gamename sent to master server in
                                      getservers[Ext] query and infoResponse
                                      "gamename" infostring value. Also used
                                      for filtering local network games.
  com_protocol                      - Specify protocol version number for
                                      current ioquake3 protocol, see
                                      "Network protocols" section below
                                      (startup only)

  in_joystickNo                     - select which joystick to use
  in_availableJoysticks             - list of available Joysticks
  in_keyboardDebug                  - print keyboard debug info

  sv_dlURL                          - the base of the HTTP or FTP site that
                                      holds custom pk3 files for your server
  sv_banFile                        - Name of the file that is used for storing
                                      the server bans

  net_ip6                           - IPv6 address to bind to
  net_port6                         - port to bind to using the ipv6 address
  net_enabled                       - enable networking, bitmask. Add up
                                      number for option to enable it:
                                      enable ipv4 networking:    1
                                      enable ipv6 networking:    2
                                      prioritise ipv6 over ipv4: 4
                                      disable multicast support: 8
  net_mcast6addr                    - multicast address to use for scanning for
                                      ipv6 servers on the local network
  net_mcastiface                    - outgoing interface to use for scan

  r_allowResize                     - make window resizable (SDL only)
  r_ext_texture_filter_anisotropic  - anisotropic texture filtering
  r_zProj                           - distance of observer camera to projection
                                      plane in quake3 standard units
  r_greyscale                       - desaturate textures, useful for anaglyph,
                                      supports values in the range of 0 to 1
  r_stereoEnabled                   - enable stereo rendering for techniques
                                      like shutter glasses (untested)
  r_anaglyphMode                    - Enable rendering of anaglyph images
                                      red-cyan glasses:    1
                                      red-blue:            2
                                      red-green:           3
                                      green-magenta:       4
                                      To swap the colors for left and right eye
                                      just add 4 to the value for the wanted
                                      color combination. For red-blue and
                                      red-green you probably want to enable
                                      r_greyscale
  r_stereoSeparation                - Control eye separation. Resulting
                                      separation is r_zProj divided by this
                                      value in quake3 standard units.
                                      See also
                                      http://wiki.ioquake3.org/Stereo_Rendering
                                      for more information
  r_marksOnTriangleMeshes           - Support impact marks on md3 models, MOD
                                      developers should increase the mark
                                      triangle limits in cg_marks.c if they
                                      intend to use this.
  r_sdlDriver                       - read only, indicates the SDL driver
                                      backend being used
  r_noborder                        - Remove window decoration from window
                                      managers, like borders and titlebar.
  r_screenshotJpegQuality           - Controls quality of jpeg screenshots
                                      captured using screenshotJPEG
  r_aviMotionJpegQuality            - Controls quality of video capture when
                                      cl_aviMotionJpeg is enabled
  r_mode -2                         - This new video mode automatically uses the
                                      desktop resolution.
```

## New commands

```
  video [filename]        - start video capture (use with demo command)
  stopvideo               - stop video capture
  stopmusic               - stop background music
  minimize                - Minimize the game and show desktop
  togglemenu              - causes escape key event for opening/closing menu, or
                            going to a previous menu. works in binds, even in UI

  print                   - print out the contents of a cvar
  unset                   - unset a user created cvar

  banaddr <range>         - ban an ip address range from joining a game on this
                            server, valid <range> is either playernum or CIDR
                            notation address range.
  exceptaddr <range>      - exempt an ip address range from a ban.
  bandel <range>          - delete ban (either range or ban number)
  exceptdel <range>       - delete exception (either range or exception number)
  listbans                - list all currently active bans and exceptions
  rehashbans              - reload the banlist from serverbans.dat
  flushbans               - delete all bans

  net_restart             - restart network subsystem to change latched settings
  game_restart <fs_game>  - Switch to another mod

  which <filename/path>   - print out the path on disk to a loaded item

  execq <filename>        - quiet exec command, doesn't print "execing file.cfg"

  kicknum <client number> - kick a client by number, same as clientkick command
  kickall                 - kick all clients, similar to "kick all" (but kicks
                            everyone even if someone is named "all")
  kickbots                - kick all bots, similar to "kick allbots" (but kicks
                            all bots even if someone is named "allbots")

  tell <client num> <msg> - send message to a single client (new to server)
```


# README for Users

## Using shared libraries instead of qvm

To force Q3 to use shared libraries instead of qvms run it with the following
parameters: `+set sv_pure 0 +set vm_cgame 0 +set vm_game 0 +set vm_ui 0`

## Using Demo Data Files

Copy demoq3/pak0.pk3 from the demo installer to your baseq3 directory. The
qvm files in this pak0.pk3 will not work, so you have to use the native
shared libraries or qvms from this project. To use the new qvms, they must be
put into a pk3 file. A pk3 file is just a zip file, so any compression tool
that can create such files will work. The shared libraries should already be
in the correct place. Use the instructions above to use them.

Please bear in mind that you will not be able to play online using the demo
data, nor is it something that we like to spend much time maintaining or
supporting.

## Help! Ioquake3 won't give me an fps of X anymore when setting com_maxfps!

Ioquake3 now uses the select() system call to wait for the rendering of the
next frame when com_maxfps was hit. This will improve your CPU load
considerably in these cases. However, not all systems may support a
granularity for its timing functions that is required to perform this waiting
correctly. For instance, ioquake3 tells select() to wait 2 milliseconds, but
really it can only wait for a multiple of 5ms, i.e. 5, 10, 15, 20... ms.
In this case you can always revert back to the old behaviour by setting the
cvar com_busyWait to 1.

## Using HTTP/FTP Download Support (Server)

You can enable redirected downloads on your server even if it's not
an ioquake3 server.  You simply need to use the 'sets' command to put
the sv_dlURL cvar into your SERVERINFO string and ensure sv_allowDownloads
is set to 1

sv_dlURL is the base of the URL that contains your custom .pk3 files
the client will append both fs_game and the filename to the end of
this value.  For example, if you have sv_dlURL set to
`"http://ioquake3.org"`, fs_game is `"baseq3"`, and the client is
missing `"test.pk3"`, it will attempt to download from the URL
`"http://ioquake3.org/baseq3/test.pk3"`

sv_allowDownload's value is now a bitmask made up of the following
flags:

  * 1 - ENABLE
  * 4 - do not use UDP downloads
  * 8 - do not ask the client to disconnect when using HTTP/FTP

Server operators who are concerned about potential "leeching" from their
HTTP servers from other ioquake3 servers can make use of the HTTP_REFERER
that ioquake3 sets which is `"ioQ3://{SERVER_IP}:{SERVER_PORT}"`.  For,
example, Apache's mod_rewrite can restrict access based on HTTP_REFERER.

On a sidenote, downloading via UDP has been improved and yields higher data
rates now. You can configure the maximum bandwidth for UDP downloads via the
cvar sv_dlRate. Due to system-specific limits the download rate is capped
at about 1 Mbyte/s per client, so curl downloading may still be faster.

## Using HTTP/FTP Download Support (Client)

Simply setting cl_allowDownload to 1 will enable HTTP/FTP downloads
assuming ioquake3 was compiled with USE_CURL=1 (the default).
like sv_allowDownload, cl_allowDownload also uses a bitmask value
supporting the following flags:

  * 1 - ENABLE
  * 2 - do not use HTTP/FTP downloads
  * 4 - do not use UDP downloads

When ioquake3 is built with USE_CURL_DLOPEN=1 (default on some platforms),
it will use the value of the cvar cl_cURLLib as the filename of the cURL
library to dynamically load.

## Multiuser Support on Windows systems
On Windows, all user specific files such as autogenerated configuration,
demos, videos, screenshots, and autodownloaded pk3s are now saved in a
directory specific to the user who is running ioquake3.

On NT-based such as Windows XP, this is usually a directory named:

    C:\Documents and Settings\%USERNAME%\Application Data\Quake3\

Windows 95, Windows 98, and Windows ME will use a directory like:

    C:\Windows\Application Data\Quake3

in single-user mode, or:

    C:\Windows\Profiles\%USERNAME%\Application Data\Quake3

if multiple logins have been enabled.

In order to access this directory more easily, the installer may create a
Shortcut which has its target set to:

    %APPDATA%\Quake3\

This Shortcut would work for all users on the system regardless of the
locale settings.  Unfortunately, this environment variable is only
present on Windows NT based systems.

You can revert to the old single-user behaviour by setting the fs_homepath
cvar to the directory where ioquake3 is installed.  For example:

    ioquake3.exe +set fs_homepath "c:\ioquake3"

Note that this cvar MUST be set as a command line parameter.

## SDL Keyboard Differences

ioquake3 clients have different keyboard behaviour compared to the original
Quake3 clients.

  * "Caps Lock" and "Num Lock" can not be used as normal binds since they
      do not send a KEYUP event until the key is pressed again.

  * SDL > 1.2.9 does not support disabling dead key recognition. In order to
      send dead key characters (e.g. ~, ', `, and ^), you must key a Space (or
      sometimes the same character again) after the character to send it on
      many international keyboard layouts.

  * The SDL client supports many more keys than the original Quake3 client.
      For example the keys: "Windows", "SysReq", "ScrollLock", and "Break".
      For non-US keyboards, all of the so called "World" keys are now supported
      as well as F13, F14, F15, and the country-specific mode/meta keys.

On many international layouts the default console toggle keys are also dead
keys, meaning that dropping the console potentially results in
unintentionally initiating the keying of a dead key. Furthermore SDL 1.2's
dead key support is broken by design and Q3 doesn't support non-ASCII text
entry, so the chances are you won't get the correct character anyway.

If you use such a keyboard layout, you can set the cvar cl_consoleKeys. This
is a space delimited list of key names that will toggle the console. The key
names are the usual Q3 names e.g. "~", "`", "c", "BACKSPACE", "PAUSE",
"WINDOWS" etc. It's also possible to use ASCII characters, by hexadecimal
number. Some example values for cl_consoleKeys:

    "~ ` 0x7e 0x60"           Toggle on ~ or ` (the default)
    "WINDOWS"                 Toggle on the Windows key
    "c"                       Toggle on the c key
    "0x43"                    Toggle on the C character (Shift-c)
    "PAUSE F1 PGUP"           Toggle on the Pause, F1 or Page Up keys

Note that when you elect a set of console keys or characters, they cannot
then be used for binding, nor will they generate characters when entering
text. Also, in addition to the nominated console keys, Shift-ESC is hard
coded to always toggle the console.

## QuakeLive mouse acceleration
(patch and this text written by TTimo from id)

I've been using an experimental mouse acceleration code for a while, and
decided to make it available to everyone. Don't be too worried if you don't
understand the explanations below, this is mostly intended for advanced
players:
To enable it, set cl_mouseAccelStyle 1 (0 is the default/legacy behavior)

New style is controlled with 3 cvars:

sensitivity
cl_mouseAccel
cl_mouseAccelOffset

The old code (cl_mouseAccelStyle 0) can be difficult to calibrate because if
you have a base sensitivity setup, as soon as you set a non zero acceleration
your base sensitivity at low speeds will change as well. The other problem
with style 0 is that you are stuck on a square (power of two) acceleration
curve.

The new code tries to solve both problems:

Once you setup your sensitivity to feel comfortable and accurate enough for
low mouse deltas with no acceleration (cl_mouseAccel 0), you can start
increasing cl_mouseAccel and tweaking cl_mouseAccelOffset to get the
amplification you want for high deltas with little effect on low mouse deltas.

cl_mouseAccel is a power value. Should be >= 1, 2 will be the same power curve
as style 0. The higher the value, the faster the amplification grows with the
mouse delta.

cl_mouseAccelOffset sets how much base mouse delta will be doubled by
acceleration. The closer to zero you bring it, the more acceleration will
happen at low speeds. This is also very useful if you are changing to a new
mouse with higher dpi, if you go from 500 to 1000 dpi, you can divide your
cl_mouseAccelOffset by two to keep the same overall 'feel' (you will likely
gain in precision when you do that, but that is not related to mouse
acceleration).

Mouse acceleration is tricky to configure, and when you do you'll have to
re-learn your aiming. But you will find that it's very much forth it in the
long run.

If you try the new acceleration code and start using it, I'd be very
interested by your feedback.


# README for Developers

## pk3dir

ioquake3 has a useful new feature for mappers. Paths in a game directory with
the extension ".pk3dir" are treated like pk3 files. This means you can keep
all files specific to your map in one directory tree and easily zip this
folder for distribution.

## 64bit mods

If you wish to compile external mods as shared libraries on a 64bit platform,
and the mod source is derived from the id Q3 SDK, you will need to modify the
interface code a little. Open the files ending in _syscalls.c and change
every instance of int to intptr_t in the declaration of the syscall function
pointer and the dllEntry function. Also find the vmMain function for each
module (usually in cg_main.c g_main.c etc.) and similarly replace the return
value in the prototype with intptr_t (arg0, arg1, ...stay int).

Add the following code snippet to q_shared.h:

    #ifdef Q3_VM
    typedef int intptr_t;
    #else
    #include <stdint.h>
    #endif

Note if you simply wish to run mods on a 64bit platform you do not need to
recompile anything since by default Q3 uses a virtual machine system.

## Creating mods compatible with Q3 1.32b

If you're using this package to create mods for the last official release of
Q3, it is necessary to pass the commandline option '-vq3' to your invocation
of q3asm. This is because by default q3asm outputs an updated qvm format that
is necessary to fix a bug involving the optimizing pass of the x86 vm JIT
compiler.

## Creating standalone games

Have you finished the daunting task of removing all dependencies on the Q3
game data? You probably now want to give your users the opportunity to play
the game without owning a copy of Q3, which consequently means removing cd-key
and authentication server checks. In addition to being a straightforward Q3
client, ioquake3 also purports to be a reliable and stable code base on which
to base your game project.

However, before you start compiling your own version of ioquake3, you have to
ask yourself: Have we changed or will we need to change anything of importance
in the engine?

If your answer to this question is "no", it probably makes no sense to build
your own binaries. Instead, you can just use the pre-built binaries on the
website. Just make sure the game is called with:

    +set com_basegame <yournewbase>
    
in any links/scripts you install for your users to start the game. The
binary must not detect any original quake3 game pak files. If this
condition is met, the game will set com_standalone to 1 and is then running
in stand alone mode.
  
If you want the engine to use a different directory in your homepath than
e.g. "Quake3" on Windows or ".q3a" on Linux, then set a new name at startup
by adding

    +set com_homepath <homedirname>

to the command line. You can also control which game name to use when talking
to the master server:

    +set com_gamename <gamename>

So clients requesting a server list will only receive servers that have a
matching game name.

Example line:

    +set com_basegame basefoo +set com_homepath .foo
    +set com_gamename foo

If you really changed parts that would make vanilla ioquake3 incompatible with
your mod, we have included another way to conveniently build a stand-alone
binary. Just run make with the option BUILD_STANDALONE=1. Don't forget to edit
the PRODUCT_NAME and subsequent #defines in qcommon/q_shared.h with
information appropriate for your project.

## Standalone game licensing

While a lot of work has been put into ioquake3 that you can benefit from free
of charge, it does not mean that you have no obligations to fulfill. Please be
aware that as soon as you start distributing your game with an engine based on
our sources we expect you to fully comply with the requirements as stated in
the GPL. That includes making sources and modifications you made to the
ioquake3 engine as well as the game-code used to compile the .qvm files for
the game logic freely available to everyone. Furthermore, note that the "QIIIA
Game Source License" prohibits distribution of mods that are intended to
operate on a version of Q3 not sanctioned by id software:

    "with this Agreement, ID grants to you the non-exclusive and limited right
    to distribute copies of the Software ... for operation only with the full
    version of the software game QUAKE III ARENA"

This means that if you're creating a standalone game, you cannot use said
license on any portion of the product. As the only other license this code has
been released under is the GPL, this is the only option.

This does NOT mean that you cannot market this game commercially. The GPL does
not prohibit commercial exploitation and all assets (e.g. textures, sounds,
maps) created by yourself are your property and can be sold like every other
game you find in stores.

## Network protocols

There are now two cvars that give you some degree of freedom over the reported
protocol versions between clients and servers: "com_protocol" and
"com_legacyprotocol".
The reason for this is that some standalone games increased the protocol
number even though nothing really changed in their protocol and the ioquake3
engine is still fully compatible.

In order to harden the network protocol against UDP spoofing attacks a new
network protocol was introduced that defends against such attacks.
Unfortunately, this protocol will be incompatible to the original quake3 1.32c
which is the latest official release from id.
Luckily, ioquake3 has backwards compatibility, on the client as well as on the
server. This means ioquake3 players can play on old servers just as ioquake3
servers are able to service old clients.

The cvar "com_protocol" denotes the protocol version for the new hardened
protocol, whereas the "com_legacyprotocol" cvar denotes the protocol version
for the legacy protocol.
If the value for "com_protocol" and "com_legacyprotocol" is identical, then
the legacy protocol is always used. If "com_legacyprotocol" is set to 0, then
support for the legacy protocol is disabled.

Mods that use a standalone engine obviously do not require dual protocol
support, and it is turned off if the engine is compiled with STANDALONE per
default. If you desire backwards compatibility to older versions of your
game you can still enable it in q_shared.h by defining
LEGACY_PROTOCOL.

## cl_guid Support

cl_guid is a cvar which is part of the client's USERINFO string.  Its value
is a 32 character string made up of [a-f] and [0-9] characters.  This
value is pseudo-unique for every player.  Id's Quake 3 Arena client also
sets cl_guid, but only if Punkbuster is enabled on the client.

If cl_guidServerUniq is non-zero (the default), then this value is also
pseudo-unique for each server a client connects to (based on IP:PORT of
the server).

The purpose of cl_guid is to add an identifier for each player on
a server.  This value can be reset by the client at any time so it's not
useful for blocking access.  However, it can have at least two uses in
your mod's game code:

  1. improve logging to allow statistical tools to index players by more
       than just name
  2. granting some weak admin rights to players without requiring passwords

## PNG support

ioquake3 supports the use of PNG (Portable Network Graphic) images as
textures. It should be noted that the use of such images in a map will
result in missing placeholder textures where the map is used with the id
Quake 3 client or earlier versions of ioquake3.

Recent versions of GtkRadiant and q3map2 support PNG images without
modification. However GtkRadiant is not aware that PNG textures are supported
by ioquake3. To change this behaviour open the file 'q3.game' in the 'games'
directory of the GtkRadiant base directory with an editor and change the
line:

    texturetypes="tga jpg"

to

    texturetypes="tga jpg png"

Restart GtkRadiant and PNG textures are now available.

## Building with MinGW for pre Windows XP

IPv6 support requires a header named "wspiapi.h" to abstract away from
differences in earlier versions of Windows' IPv6 stack. There is no MinGW
equivalent of this header and the Microsoft version is obviously not
redistributable, so in its absence we're forced to require Windows XP.
However if this header is acquired separately and placed in the qcommon/
directory, this restriction is lifted.


# Contributing

Please send all patches to bugzilla (https://bugzilla.icculus.org), or join the
mailing list (http://lists.ioquake.org/listinfo.cgi/ioquake3-ioquake.org) and 
submit your patch there.  The best case scenario is that you submit your patch 
to bugzilla, and then post the URL to the mailing list.

The focus for ioq3 is to develop a stable base suitable for further development
and provide players with the same Quake 3 experience they've had for years. As
such ioq3 does not have any significant graphical enhancements and none are
planned at this time. However, improved graphics and sound patches will be
accepted as long as they are entirely optional, do not require new media and
are off by default.


# Building Official Installers

We need help getting automated installers on all the platforms that ioquake3
supports. We don't necessarily care about all the installers being identical,
but we have some general guidelines:

  * Please include the id patch pk3s in your installer, which are available
    from http://ioquake3.org/patch-data/ subject to agreement to the id
    EULA. Your installer shall also ask the user to agree to this EULA (which
    is in the /web/include directory for your convenience) and subsequently
    refuse to continue the installation of the patch pk3s and pak0.pk3 if they
    do not.

  * Please don't require pak0.pk3, since not everyone using the engine
    plans on playing Quake 3 Arena on it. It's fine to (optionally) assist the
    user in copying the file or tell them how.

  * It is fine to just install the binaries without requiring id EULA agreement,
    providing pak0.pk3 and the patch pk3s are not referred to or included in the
    installer.

  * Please include at least an SDL so/dylib/dll on every platform.

  * Please include an OpenAL so/dylib/dll, since every platform should be using
    it by now.

  * Please contact the mailing list when you've made your installer.

  * Please be prepared to alter your installer on the whim of the maintainers.

  * Your installer will be mirrored to an "official" directory, thus making it
    a done deal.


# Credits

Maintainers

  * James Canete <use.less01@gmail.com>
  * Ludwig Nussel <ludwig.nussel@suse.de>
  * Thilo Schulz <arny@ats.s.bawue.de>
  * Tim Angus <tim@ngus.net>
  * Tony J. White <tjw@tjw.org>
  * Zachary J. Slater <zachary@ioquake.org>
  * Zack Middleton <zturtleman@gmail.com>

Significant contributions from

  * Ryan C. Gordon <icculus@icculus.org>
  * Andreas Kohn <andreas@syndrom23.de>
  * Joerg Dietrich <Dietrich_Joerg@t-online.de>
  * Stuart Dalton <badcdev@gmail.com>
  * Vincent S. Cojot <vincent at cojot dot name>
  * optical <alex@rigbo.se>
  * Aaron Gyes <floam@aaron.gy>

