# autodl-curl-pvr

Script for autodl-irssi/ruTorrent to send uploads quickly to Sonarr v2, Radarr v2, and Lidarr (hereinafter called PVRs). 

No more waiting for RSS to update.

## You will need:
autodl-irssi (https://github.com/autodl-community) or ruTorrent with Autodl-IRSSI

Sonarr (https://sonarr.tv/)

Radarr (https://radarr.video/)

Lidarr (https://lidarr.audio/)

curl

```(Each PVR is optional)```

## Download:
```
$ wget https://github.com/trinsic/autodl-curl-pvr/raw/master/autodl-curl-pvr.sh
$ chmod +x autodl-curl-pvr.sh
```

# Configure

## Step 1: Setup script
PVRs are set to localhost along with their default ports. Change as necessary in the get_api_url block (line 23/25/27).
```
    if [ "$pvr" == "lidarr" ]; then
        apiUrl="http://localhost:8686/api/v1/release/push"
    elif [ "$pvr" == "radarr" ]; then
        apiUrl="http://localhost:7878/api/release/push"
    elif [ "$pvr" == "sonarr" ]; then
        apiUrl="http://localhost:8989/api/release/push"
```

Each PVR comes with an API key required for the script to work. You can find these under Settings > General in each PVR. Copy each one to the respective line in the script (line 38/40/42).
```
    if [ "$pvr" == "lidarr" ]; then
        apiKey="lidarr key goes here"
    elif [ "$pvr" == "radarr" ]; then
        apiKey="radarr key goes here"
    elif [ "$pvr" == "sonarr" ]; then
        apiKey="sonarr key goes here"
```

## Step 2: Setup Autodl
If you want a catch-all approach then configure `autodl.cfg` with the global `[options]`:
```
upload-type = exec
upload-command = /path/to/autodl-curl-pvr.sh
upload-args = "$(FilterName)" "$(TorrentName)" "$(TorrentUrl)" "$(Tracker)"
```
Your filter names must be set as sonarr, radarr, and lidarr for this to work. You can set it to match everything and let your PVRs decide.

```
[filter sonarr]
download-duplicates = true
match-categories = *tv*

[filter radarr]
download-duplicates = true
match-categories = *mov*

[filter lidarr]
download-duplicates = true
match-categories = *mus*
```
or if you need individual settings and more control you can set specific PVRs and add the exec cmd to your `[filter]`:

Sonarr example
```
[filter BTN to Sonarr]
match-sites = btn
upload-type = exec
upload-command = /path/to/autodl-curl-pvr.sh
upload-args = "sonarr" "$(TorrentName)" "$(TorrentUrl)" "$(Tracker)"
```
This will send all BTN uploads to Sonarr and it will decide what to download based on your monitored content.

The rest is up to you, Autodl has many ways of being set up.

## Testing
You can do dry runs straight from the command line and check the logs in each PVR to see if they work.

`./autodl-curl-pvr.sh "sonarr" "test" "http://test.com" "SomeTracker"`


> Original script based off Zymest (repo missing), modified to work for all PVR's at once.
