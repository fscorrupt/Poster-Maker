# Plex Poster Maker

This PowerShell script automates the process of generating posters for your Plex media library. Leveraging information from your Plex library, such as movie or show titles, it fetches relevant artwork from Fanart.tv, TMDB, TVDB, and IMDB. The script is able to focus on specific language to grab; by default, it is `xx`, which means textless, and then fallbacks to `en` if not available. This is a setting a user can decide on, either to focus on textless or on text posters. It also offers both automatic and manual modes for generating posters, accommodating custom creations that cannot be automated.

### ! Important !
The `Temp` Folder gets cleared on every Script run, so do not put files into it.


**Key Features:**
- **Resizing**: It automatically resizes every poster to 2000x3000.
- **Overlays**: If you choose to, downloaded images will automatically have borders, text, and a gradient overlay applied.
- **Automatic Library Search**: The script autonomously searches for libraries within your Plex server, enhancing its usability.
- **Handling Multiple Versions**: It adeptly manages multiple versions of a movie/show, ensuring comprehensive coverage.
- **CSV Export**: Produces an impressive CSV file containing all queried movie/show information during the script's runtime in `$ScriptRoot\logs\PlexLibexport.csv`
- **Logging Capabilities**: Records valuable information to a file in `$ScriptRoot\logs\Scriptlog.log`, facilitating troubleshooting and analysis.
    
    - It also generates a log with the output of every imagemagick command `$ScriptRoot\logs\ImageMagickCommands.log`.
    - Additionally, an `ImageChoices.csv` file is generated to store all the selected download options and essential information.
- **Cross-platform Compatibility**: Ensures seamless operation across Linux, Docker, and Windows Plex servers, enhancing versatility.
- **Poster/Background Creation**: It searches fanart/tmdb/tvdb for textless posters/backgrounds and resizes the downloaded image to 3840x2160, fallback is grabbing a poster from imdb.

Upon initial execution, the script may take some time to run as it compiles necessary data. Subsequent runs efficiently create missing posters, bypassing existing assets in the directory.

**Requirements:**
Before utilizing the script, ensure the following prerequisites are installed and configured:

- **TMDB API Read Access Token:** [Obtain TMDB API Token](https://www.themoviedb.org/settings/api)
- **Fanart Personal API Key:** [Obtain Fanart API Key](https://fanart.tv/get-an-api-key)
- **TVDB API Key:** [Obtain TVDB API Key](https://thetvdb.com/api-information/signup)
    - **Do not** use `"Legacy API Key"`, it only works with a Project Api Key.
- **ImageMagick:** [Download ImageMagick](https://imagemagick.org/script/download.php#windows)
    - Download and Install the Latest Imagemagick, installation is covered in Script **(You may need to run the Script as Admin on first run)**.
- **Powershell Version:** 5.x or higher, ps core 6.x/7.x also works.

## Configuration:

1. Open `config.example.json` located in the script directory.
2. Update the following variables with your API keys and preferences:

**ApiPart:**

- `tvdbapi`: Your TVDB project API key.
- `tmdbtoken`: Your TMDB token.
- `FanartTvAPIKey`: Your Fanart personal API key.
- `PlexToken`: Leave empty if not applicable.
- `FavProvider`: Set your preferred provider (default is `tmdb`).
- `PreferedLanguageOrder`: Specify language preferences. Default is `xx,en,de` (`xx` is Textless). Example configurations can be found in the config file. 2-digit language codes can be found here: [ISO 3166-1 Lang Codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2).

**PlexPart:**

- `LibstoExclude`: Libraries to exclude from processing.
- `PlexUrl`: Plex server URL.

**PrerequisitePart:**

- `AssetPath`: Path to store generated posters.
- `magickinstalllocation`: Path to ImageMagick installation location where `Magick.exe` is located.
- `font`: Font file name.
- `backgroundfont`: Background font file name.
- `overlayfile`: Overlay file name.
- `backgroundoverlayfile`: Background overlay file name.
- `LibraryFolders`: Set to `true` for asset structure in one flat folder or `false` to split into library media folders like [Plex-Meta-Manager](https://metamanager.wiki/en/latest/pmm/guides/assets/#image-asset-directory-guide) needs it.
- `SeasonPosters`: Set to `true` to also create season posters.
- `BackgroundPosters`: Set to `true` to also create background posters.

**OverlayPart:**

- `ImageProcessing`: Set to `true` if you want the ImageMagick part; if false, it only downloads the posters.

**PosterOverlayPart:**

- `fontAllCaps`: Set to `true` for all caps text, else `false`.
- `AddBorder`: Set to `true` to add a border to the image.
- `AddText`: Set to `true` to add text to the image.
- `AddOverlay`: Set to `true` to add the defined overlay file to the image.
- `fontcolor`: Color of font text.
- `bordercolor`: Color of border.
- `minPointSize`: Minimum size of text in poster.
- `maxPointSize`: Maximum size of text in poster.
- `borderwidth`: Border width.
- `MaxWidth`: Maximum width of text box.
- `MaxHeight`: Maximum height of text box.
- `text_offset`: Text box offset from the bottom of the picture.

**BackgroundOverlayPart:**

- `fontAllCaps`: Set to `true` for all caps text, else `false`.
- `AddBorder`: Set to `true` to add a border to the background image.
- `AddText`: Set to `true` to add text to the background image.
- `AddOverlay`: Set to `true` to add the defined background overlay file to the background image.
- `fontcolor`: Color of font text.
- `bordercolor`: Color of border.
- `minPointSize`: Minimum size of text in background image.
- `maxPointSize`: Maximum size of text in background image.
- `borderwidth`: Border width.
- `MaxWidth`: Maximum width of text box in background image.
- `MaxHeight`: Maximum height of text box in background image.
- `text_offset`: Text box offset from the bottom of the background image.

3. Rename the config file to `config.json`.
4. Place the `overlay.png`, or whatever file you defined earlier in `overlayfile`, and `Rocky.ttf` font, or whatever font you defined earlier in `font` files in the same directory as PosterMaker.ps1 which is `$ScriptRoot`.

**Usage:**
- **Automatic Mode**: Execute the script without any parameters to generate posters for your entire Plex library.
- **Testing Mode**: Run the script with the `-Testing` switch to create Test posters before you start using it.
- **Manual Mode**: Run the script with the `-Manual` switch to create custom posters manually.

**Note:**
- Ensure PowerShell execution policy allows script execution.
- Bugs or issues encountered during usage can be reported for resolution.

Feel free to customize the script further to meet your specific preferences or automation requirements.

### Automatic Mode

Run the script without any parameters:

```powershell
.\PlexPosterMaker.ps1
```

This will generate posters for your entire Plex library based on the configured settings.

The posters are all placed in `AssetPath\...`. This can then be mounted in Plex-Meta-Manager to use as the assets folder.

### Testing Mode

Run the script with the `-Testing` flag. In this mode, the script will create pink posters/backgrounds with short, medium, and long texts (also in CAPS), using the values specified in the `config.json` file. 

This is handy for testing your configuration before applying it en masse to the actual posters. You can see how and where the text would be applied, as well as the size of the textbox.

```powershell
.\PlexPosterMaker.ps1 -Testing
```
![TestMode](/images/testing.png)

### Manual Mode

Run the script with the `-Manual` switch:

```powershell
.\PlexPosterMaker.ps1 -Manual
```

Follow the prompts to enter the source picture path, media folder name, and movie/show title to manually create a custom poster.


### Example of Script folder:
![scriptimage](images/folder.png)

### Example of script output:
![outputimage](images/output.png)

### Example of ImageChoices.csv:
![outputimage](images/imagecsv.png)

### Example of Movie Posters after creation:
![assetimage](images/movies.png)

### Example of Show Posters after creation:
![assetimage](images/shows.png)

### Example of 4K Lib after Plex-Meta-Manager magic:
![4kimage](images/movies4kpmm.png)

### Example of Movie Lib after Plex-Meta-Manager magic:
![movieimage](images/movies_pmm.png)

### Example of TV Lib after Plex-Meta-Manager magic:
![tvimage](images/showspmm.png)

### Example of TV Lib Seasons after Plex-Meta-Manager magic:
![seasonimage](images/seasonspmm.png)


Feel free to customize the script further to suit your preferences or automation needs.
