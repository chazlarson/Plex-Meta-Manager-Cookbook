Relevant settings to start:

```
libraries:
  TV Shows:
    collection_files:
        - file: config/testing.yml
    settings:
        asset_directory: config/assets/TV

    operations:
        assets_for_all: false
#         mass_poster_update:
#             source: lock
#             seasons: false
#             episodes: false

settings:
  asset_directory: config/hampus-assets
  asset_folders: false
  create_asset_folders: false
  download_url_assets: false
```

`testing.yml`:
```
templates:
  alphabetic_collection:
    tvdb_show: <<show>>
    url_poster: <<poster>>

collections:
  poster YES:
    template: {name: alphabetic_collection, show: "337907, 364080", poster: https://theposterdb.com/api/assets/203792}
    
  poster NO:
    template: {name: alphabetic_collection, show: "277092, 332747"}
```

Library has no collections.

I've created `config/assets`

Every run is this command:
```
python plex_meta_manager.py --run --config config/test-config.yml -dc
```

### Run 01 changes:

None

### Run 01 result:

nothing downloaded, no folders created

### Run 02 changes:

changed:
```
  download_url_assets: true
```
### Run 02 result:

nothing downloaded

### Run 03 changes:

changed:
```
  asset_folders: true
```
### Run 03 result:

nothing downloaded

### Run 04 changes:

changed:
```
  create_asset_folders: true
```
### Run 04 result:

created `config/hampus-assets/poster NO`
created `config/hampus-assets/poster YES`
downloaded `https://theposterdb.com/api/assets/203792` to `config/hampus-assets/poster YES/poster.jpg`

### Run 05 changes:

delete `poster.jpg`
change:
```
  create_asset_folders: false
```

### Run 05 result:

downloaded `https://theposterdb.com/api/assets/203792` to `config/hampus-assets/poster YES/poster.jpg`

### Run 06 changes:

delete `poster.jpg`
change:
```
  asset_folders: false
```
### Run 06 result:

nothing downloaded

So there's something we've learned:
```
 download_url_assets: true
```
REQUIRES `asset_folders: true`

### Run 07 changes:

change:
```
        assets_for_all: true
```
### Run 07 result:

nothing downloaded
no folders created

### Run 08 changes:

change:
```
  asset_folders: true
```
### Run 08 result:

nothing downloaded
no folders created

### Run 09 changes:

changed:
```
  asset_directory:
```
### Run 09 result:

nothing created or downloaded

### Run 10 changes:

manually created: `config/hampus-assets/TV`

### Run 10 result:

created `config/hampus-assets/TV/poster NO`
created `config/hampus-assets/TV/poster YES`
downloaded `https://theposterdb.com/api/assets/203792` to `config/hampus-assets/TV/poster YES/poster.jpg`

So there's something we've learned:
PMM will not create asset directories, only directories WITHIN asset directories 

### Run 11 changes:

uncomment:
```
        mass_poster_update:
            source: lock
            seasons: false
            episodes: false
```
### Run 11 result:

3500 folders created [one for every show in the library]

So there's something we've learned:
running a mass poster update, even if it doesn't set the art, triggers the "look for an asset and create the folder if needed and enabled" logic

### Run 12 changes:

delete all 3500 movie asset folders
change:
```
        assets_for_all: true
```
### Run 12 result:

3500 folders created [one for every show in the library]

So there's something we've learned:
mass poster reset creating asset folders does not depend on `assets_for_all`

Summary:

1. PMM will only download `url_poster` if `asset_folders` is enabled
1. PMM will create all missing asset folders if `create_asset_folders` is enabled, not just those that it is downloading
1. If `create_asset_folders` is disabled and the asset folder does not exist, PMM will not download a `url_poster`
1. Performing a mass poster update will create asset folders for everything in the library if `create_asset_folders` is enabled, regardless of `assets_for_all` setting.
