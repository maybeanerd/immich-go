# Immich-Go: Upload Your Photos to Your Immich Server

**Immich-Go** is an open-source tool designed to streamline uploading large photo collections to your self-hosted Immich server.

![screen](/docs/v.20.gif)


## You can now support my work on `Immich-go`:

- [Github Sponsor page](https://github.com/sponsors/simulot)
- [paypal donor page](https://www.paypal.com/donate/?hosted_button_id=VGU2SQE88T2T4)


## Key Features:

* **Effortlessly Upload Large Google Photos Takeouts:**  Immich-Go excels at handling the massive archives you download from Google Photos using Google Takeout. It efficiently processes these archives while preserving valuable metadata like GPS location, date taken, and album information.
* **Flexible Uploads:**  Immich-Go isn't limited to Google Photos. You can upload photos directly from your computer folders, folders tree and ZIP archives.
* **Simple Installation:** Immich-Go doesn't require NodeJS or Docker for installation. This makes it easy to get started, even for those less familiar with technical environments.
* **Prioritize Quality:**  Immich-Go discards any lower-resolution versions that might be included in Google Photos Takeout, ensuring you have the best possible copies on your Immich server.
* **Stack burst and raw/jpg photos**: Group together related photos in Immich.


## Google Photos Best Practices:

* **Taking Out Your Photos:**
  * Choose the ZIP format when creating your takeout for easier import.
  * Select the largest file size available (50GB) to minimize the number of archive parts.
  * Download all parts on your computer

* **Importing Your Photos:**
  * If your takeout is in ZIP format, you can import it directly without needing to unzip the files first.
  * It's important to import all the parts of the takeout together, since some data might be spread across multiple files. 
    <br>Use `/path/to/your/files/takeout-*.zip` as file name.
  * For **.tgz** files (compressed tar archives), you'll need to decompress all the files into a single folder before importing. When using the import tool, don't forget the `-google-photos` option.
  * You can remove any unwanted files or folders from your takeout before importing. 
  * Restarting an interrupted import won't cause any problems and it will resume the work where it was left.

* **Lot of files are not imported**: What are the options?
  * Verify if all takeout parts have been included in the processing.
  * Request another takeout, either for an entire year or in smaller increments.
  * Force the import of files despite the missing JSON. Use the option `-upload-when-missing-JSON`

For insights into the reasoning behind this alternative to `immich-cli`, please read the motivation [here](docs/motivation.md).


> ⚠️ This an early version, not yet extensively tested<br>
> ⚠️ Keep a backup copy of your files for safety<br>


# Executing `immich-go`
The `immich-go` is a command line tool that must be run from a terminal window.  
The `immich-go` program uses the Immich API. Hence it needs the server address and a valid API key.

**On linux, MacOS, FreeBSD and linux friends:**

```sh
./immich-go -server URL -key KEY -general_options COMMAND -command_options... {path/to/files}
```

**On Windows systems:**
```powershell
.\immich-go -server URL -key KEY -general_options COMMAND -command_options... {path/to/files}
```

## How boolean options are handled

Boolean options have a default value indicated below. Mentioning any option on the common line changes the option to TRUE.
to force an option to FALSE, use the following syntax: `-option=FALSE`.

Example: Immich-go check the server's SSL certificate. you can disable this behavior by turning on the `skip-verify-ssl` option. Just add `-skip-verify-ssl`.
`-skip-verify-ssl` is equivalent to `-skip-verify-ssl=TRUE`. To turn off the feature (which is the default behavior), use `-skip-verify-ssl=FALSE`

## Shared options with all commands

| **Parameter**                            | **Description**                                                                                                                                                               | **Default value**                                                                                                                                                                                                      |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-use-configuration=path/to/config/file` | Specifies the configuration file to use. <br>Server URL and the API key are stored into the immich-go configuration file. They can be omitted for the next runs.              | Linux `$HOME/.config/immich-go/immich-go.json`<br>Windows `%AppData%\immich-go\immich-go.json`<br>Apple `$HOME/Library/Application Support/immich-go/immich-go.json`                                                   |
| `-server=URL`                            | URL of the Immich service, example http://<your-ip>:2283 or https://your-domain                                                                                               |                                                                                                                                                                                                                        |
| `-api=URL`                               | URL of the Immich api endpoint (http://container_ip:3301)                                                                                                                     |                                                                                                                                                                                                                        |
| `-device-uuid=VALUE`                     | Force the device identification                                                                                                                                               | `$HOSTNAME`                                                                                                                                                                                                            |
| `-client-timeout=duration`               | Set the timeout for server calls. The duration is a decimal number with a unit suffix, such as "300ms", "1.5m" or "45m". Valid time units are "ms", "s", "m", "h".            | `5m`                                                                                                                                                                                                                   |
| `-skip-verify-ssl`                       | Skip SSL verification for use with self-signed certificates                                                                                                                   | `false`                                                                                                                                                                                                                |
| `-key=KEY`                               | A key generated by the user. Uploaded photos will belong to the key's owner.                                                                                                  |                                                                                                                                                                                                                        |
| `-log-level=LEVEL`                       | Adjust the log verbosity as follows: <br> - `ERROR`: Display only errors  <br>  - `WARNING`: Same as previous one plus non-blocking error <br> - `INFO`: Information messages | `INFO`                                                                                                                                                                                                                 |
| `-log-file=/path/to/log/file`            | Write all messages to a file                                                                                                                                                  | Linux `$HOME/.cache/immich-go/immich-go_YYYY-MM-DD_HH-MI-SS.log` <br>Windows `%LocalAppData%\immich-go\immich-go_YYYY-MM-DD_HH-MI-SS.log` <br>Apple `$HOME/Library/Caches/immich-go/immich-go_YYYY-MM-DD_HH-MI-SS.log` |
| `-log-json`                              | Output the log as line-delimited JSON file                                                                                                                                    | `false`                                                                                                                                                                                                                |
| `-time-zone=time_zone_name`              | Set the time zone for dates without time zone information                                                                                                                     | the system's time zone                                                                                                                                                                                                 |
| `-no-ui`                                 | Disable the user interface                                                                                                                                                    | `false`                                                                                                                                                                                                                |
| `-debug-counters`                        | Enable the generation a CSV beside the log file                                                                                                                               | `false`                                                                                                                                                                                                                |
| `-api-trace`                             | Enable trace of API calls                                                                                                                                                     | `false`                                                                                                                                                                                                                |

## Command `upload`

Use this command for uploading photos and videos from a local directory, a zipped folder or all zip files that google photo takeout procedure has generated.

### Switches and options:

| **Parameter**                        | **Description**                                                                                    | **Default value**                                                                         |
| ------------------------------------ | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `-album="ALBUM NAME"`                | Import assets into the Immich album `ALBUM NAME`.                                                  |                                                                                           |
| `-dry-run`                           | Preview all actions as they would be done.                                                         | `FALSE`                                                                                   |
| `-create-album-folder`               | Generate immich albums after folder names.                                                         | `FALSE`                                                                                   |
| `-create-stacks`                     | Stack jpg/raw or bursts.                                                                           | `FALSE`                                                                                   |
| `-stack-jpg-raw`                     | Control the stacking of jpg/raw photos.                                                            | `FALSE`                                                                                   |
| `-stack-burst`                       | Control the stacking bursts.                                                                       | `FALS`                                                                                    |
| `-select-types=".ext,.ext,.ext..."`  | List of accepted extensions.                                                                       |                                                                                           |
| `-exclude-types=".ext,.ext,.ext..."` | List of excluded extensions.                                                                       |                                                                                           |
| `-when-no-date=FILE\|NOW`            | When the date of take can't be determined, use the FILE's date or the current time NOW.            | `FILE`                                                                                    |
| `-exclude-files=pattern`             | Ignore files based on a pattern. Case insensitive. Repeat the option for each pattern do you need. | `@eaDir/`<br>`@__thumb/`<br>`SYNOFILE_THUMB_*.*`<br>`Lightroom Catalog/`<br>`thumbnails/` |

### Date selection:
Fine-tune import based on specific dates:

| **Parameter**      | **Description**                                |
| ------------------ | ---------------------------------------------- |
| `-date=YYYY-MM-DD` | import photos taken on a particular day.       |
| `-date=YYYY-MM`    | select photos taken during a particular month. |
| `-date=YYYY`       | select photos taken during a particular year.  |

### Exclude files based on a pattern

Use the `-exclude-files=PATTERN` to exclude certain files or directories from the upload. Repeat the option for each pattern do you need. The following directories are excluded automatically:
- @eaDir/
- @__thumb/
- SYNOFILE_THUMB_\*.\*
- Lightroom Catalog/
- thumbnails/
- .DS_Store/


Example, the following command excludes any files in directories called backup or draft and any file with name finishing with "copy)" as PXL_20231006_063121958 (another copy).jpg:
```sh
immich-go -sever=xxxxx -key=yyyyy upload -exclude-files=backup/ -exclude-files=draft/ -exclude=copy).*  /path/to/your/files
```

### Google photos options:
Specialized options for Google Photos management:

| **Parameter**                      | **Description**                                                                  | **Default value** |
| ---------------------------------- | -------------------------------------------------------------------------------- | ----------------- |
| `-google-photos`                   | import from a Google Photos structured archive, recreating corresponding albums. |                   |
| `-from-album="GP Album"`           | Create the album in `immich` and import album's assets.                          |                   |
| `-create-albums`                   | Controls creation of Google Photos albums in Immich.                             | `TRUE`            |
| `-keep-untitled-albums`            | Untitled albums are imported into `immich` with the name of the folder as title. | `FALSE`           |
| `-use-album-folder-as-name`        | Use the folder's name instead of the album title.                                | `FALSE`           |
| `-keep-partner`                    | Specifies inclusion or exclusion of partner-taken photos.                        | `TRUE`            |
| `-partner-album="partner's album"` | import assets from partner into given album.                                     |                   |
| `-discard-archived`                | don't import archived assets.                                                    | `FALSE`           |
| `-upload-when-missing-JSON`        | Upload photos not associated with a JSON metadata file                           | `FALSE`           |

Read [here](docs/google-takeout.md) to understand how Google Photos takeout isn't easy to handle.

### Burst detection
Currently the bursts following this schema are detected:
- xxxxx_BURSTnnn.*
- xxxxx_BURSTnnn_COVER.*
- xxxxx.RAW-01.COVER.jpg and xxxxx.RAW-02.ORIGINAL.dng
- xxxxx.RAW-01.MP.COVER.jpg and xxxxx.RAW-02.ORIGINAL.dng
- xxxxxIMG_xxxxx_BURSTyyyymmddhhmmss.jpg and xxxxxIMG_xxxxx_BURSTyyyymmddhhmmss_COVER.jpg (Huawei Nexus 6P)
- yyyymmdd_hhmmss_xxx.jpg (Samsung)

All images must be taken during the same minute.
The COVER image will be the parent image of the stack

### Couple jpg/raw detection
Both images should be taken in the same minute.
The JPG image will be the cover. 

Please open an issue to cover more possibilities.

### Example Usage: uploading a Google photos takeout archive

To illustrate, here's a command importing photos from a Google Photos takeout archive captured between June 1st and June 30th, 2019, while auto-generating albums:

```sh
./immich-go -server=http://mynas:2283 -key=zzV6k65KGLNB9mpGeri9n8Jk1VaNGHSCdoH1dY8jQ upload
-create-albums -google-photos -date=2019-06 ~/Download/takeout-*.zip             
```

 
### Metadata determination

Immich-go get the first available data in the following order.

#### Date of capture:

* Google photos takeout
    1. Google photos JSON field `photoTakenTime`
    1. Photo's file name: ex `PXL_20220909_154515546.jpg`
    1. Photo's exif data
* Folder import
    1. XMP file
    1. Photo's file name: ex `PXL_20220909_154515546.jpg`
    1. Photo's file path: ex `/photos/2022/11/09/IMG_1234.HEIC`
    1. Photo's exif data 

#### GPS location:

* Google photos takeout
    1. Google photos JSON field `geoDataExif`
    1. Google photos JSON field `geoData`
    1. Google photos album JSON field `locationEnrichment`
    1. Photo's exif data 
* Folder import
    1. XMP file
    1. Photo's exif data 




#### When importing a Google Photos takeout archive:
 `immich-go` takes the photo's date from the associated JSON file.

> The server ignores the date provided by immich-go and takes the MP4's date even when it is incorrect. 
> <br>See [#322 Creation timestamp from metadata is wrong](https://github.com/simulot/immich-go/issues/332)

#### When importing photos from a directory:

Immich-go tries to determine the date of capture with the file name, or the file path.

Ex:
| Path                                    | Photo's capture date |
| --------------------------------------- | -------------------- |
| photos/album/PXL_20220909_154515546.jpg | 2022-09-09 15:51:55  |
| photos/scanned/19991231.jpg             | 1999-12-31 00:00:00  |
| photos/20221109/IMG_1234.HEIC           | 2022-11-19 00:00:00  |
| photos/2022.11.09T20.30/IMG_1234.HEIC   | 2022-11-19 20:30:00  |
| photos/2022/11/09/IMG_1234.HEIC         | 2022-11-19 00:00:00  |

If the path can't be used to determine the capture date, immich-go read the file's `metadata` or `exif`.




## Command `duplicate`

Use this command for analyzing the content of your `immich` server to find any files that share the same file name, the  date of capture, but having different size. 
Before deleting the inferior copies, the system gets all albums they belong to, and add the superior copy to them.

### Switches and options:
| **Parameter**       | **Description**                                             | **Default value**       |
| ------------------- | ----------------------------------------------------------- | ----------------------- |
| `-yes`              | Assume Yes to all questions                                 | `FALSE`                 |
| `-date`             | Check only assets have a date of capture in the given range | `1850-01-04,2030-01-01` |
| `-ignore-tz-errors` | Ignore timezone difference when searching for duplicates    | `FALSE`                 |
| `-ignore-extension` | Ignore filetype extensions when searching for duplicates    | `FALSE`                 |

### Example Usage: clean the `immich` server after having merged a google photo archive and original files

This command examines the immich server content, remove less quality images, and preserve albums.

```sh
./immich-go -server=http://mynas:2283 -key=zzV6k65KGLNB9mpGeri9n8Jk1VaNGHSCdoH1dY8jQ duplicate -yes
```

## Command `stack`

The possibility to stack images has been introduced with `immich` version 1.83. 
Let use it to group burst  and jpg/raw images together.

### Switches and options:
| **Parameter**      | **Description**                                             | **Default value**       |
| ------------------ | ----------------------------------------------------------- | ----------------------- |
| `-yes`             | Assume Yes to all questions                                 | `FALSE`                 |
| `-date=date_range` | Check only assets have a date of capture in the given range | `1850-01-04,2030-01-01` |


## Command `tool`

This command introduces command line tools to manipulate your `immich` server

### Sub command `album delete [regexp]`

This command deletes albums that match with the given pattern

#### Switches 
`-yes` Assume Yes to all questions (default: FALSE).<br> 

#### Example

```sh
./immich-go -server=http://mynas:2283 -key=zzV6k65KGLNB9mpGeri9n8Jk1VaNGHSCdoH1dY8jQ tool album delete \d{4}-\d{2}-\d{2}
```
This command deletes all albums created with de pattern YYYY-MM-DD


# Installation

## Installation from the github release:

Installing `immich-go` is a straightforward process. Visit the [latest release page](https://github.com/simulot/immich-go/releases/latest) and select the binary file compatible with your system:

- Darwin arm-64, x86-64
- Linux arm-64, armv6-64, x86-64
- Windows arm-64, x86-64
- Freebsd arm-64, x86-64

Download the archive corresponding to your OS/Architecture on your machine and decompress it. 

Open a command windows, go to the directory where immich-go resides, and type the command `immich-go` with mandatory parameters and command.

⚠️ Please note that the linux x86-64 version is the only one tested.


## Installation from sources

For a source-based installation, ensure you have the necessary Go language development tools (https://go.dev/doc/install) in place.
Download the source files or clone the repository. 

```bash
go build -ldflags "-X 'main.version=$(git describe --tag)' -X 'main.date=$(date)'"
```

## Installation with Nix

`immich-go` is packaged with [nix](https://nixos.org/) and distributed via [nixpkgs](https://search.nixos.org/packages?channel=unstable&type=packages&query=immich-go).
You can try `immich-go` without installing it with:

```bash
nix-shell -I "nixpkgs=https://github.com/NixOS/nixpkgs/archive/nixos-unstable-small.tar.gz" -p immich-go
# Or with flakes enabled
nix run "github:nixos/nixpkgs?ref=nixos-unstable-small#immich-go" -- -help
```

Or you can add `immich-go` to your `configuration.nix` in the `environment.systemPackages` section.

# Acknowledgments

Kudos to the Immich team for their stunning project! 🤩

This program use following 3rd party libraries:
- [https://github.com/rivo/tview](https://github.com/rivo/tview) the terminal user interface
- [github.com/rwcarlsen/goexif](github.com/rwcarlsen/goexif) to get date of capture from JPEG files
-	[github.com/thlib/go-timezone-local](github.com/thlib/go-timezone-local) for its windows timezone management

A big thank you to the project contributors:
- [rodneyosodo](https://github.com/rodneyosodo) gitub CI, go linter, and advice 
- [sigmahour](https://github.com/sigmahour) SSL management
- [mrwulf](https://github.com/mrwulf) Partner sharing album
- [erkexzcx](https://github.com/erkexzcx) Date determination based on file path and file name

