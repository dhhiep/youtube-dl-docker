# Docker Youtube-DL Alpine

*Download with [**youtube-dl**](https://github.com/rg3/youtube-dl) using command line arguments or configuration files*

## Features

- Works with command line arguments to *youtube-dl*
- Compatible with AMD64, 386, ARM v6/v7/v8 CPU architectures
- Small Docker image based on
    - [Alpine 3.12](https://alpinelinux.org)
    - [Youtube-dl](https://github.com/rg3/youtube-dl)
    - [ffmpeg 4.3.1](https://pkgs.alpinelinux.org/package/v3.10/community/x86_64/ffmpeg)
    - [Ca-Certificates](https://pkgs.alpinelinux.org/package/v3.10/main/x86_64/ca-certificates)
    - [Python 3.8.5](https://pkgs.alpinelinux.org/package/v3.10/main/x86_64/python)
- The container updates youtube-dl at launch if `-e AUTOUPDATE=yes`
- A log file of youtube-dl execution is saved at `downloads/log.txt` if `-e LOG=yes`
- Docker healthcheck downloading `https://duckduckgo.com` with `wget` every 10 minutes
- The Docker Hub image is updated automatically every 3 days, so simply update your image with `docker pull hoanghiepitvnn/youtube-dl-aio`
- You can receive a notification on your Android device through Gotify when the *youtube-dl* has finished

## Build and push to Docker Hub

```
docker-compose up --build
docker push hoanghiepitvnn/youtube-dl-aio
```

## Setup

**1. Add alias commands below to ~/.bashrc or ~/.zshrc**

- Change your folder downloads at `YOUTUBE_DOWNLOAD_PATH`

```
export YOUTUBE_DOWNLOAD_PATH=/Users/hiepdinh/Data/Youtube

alias yt_files="open $YOUTUBE_DOWNLOAD_PATH"
alias yt_log="tail -f $YOUTUBE_DOWNLOAD_PATH/log.txt"
alias ytdl_base="docker run --rm --name=youtube-dl -e AUTOUPDATE=yes -v $YOUTUBE_DOWNLOAD_PATH:/downloads:rw hoanghiepitvnn/youtube-dl-aio"
alias yt_audio="ytdl_base -x --audio-format mp3 --audio-quality 0 --ignore-errors"
alias yt_video="ytdl_base -f 'bestvideo[height>=1000+height<1100]+bestaudio/bestvideo[ext=mp4]' --recode-video mp4 --postprocessor-args '-vcodec copy'"
alias yt_video2k="ytdl_base -f 'bestvideo[height>=1400+height<1500]+bestaudio/bestvideo[ext=mp4]' --recode-video mp4 --postprocessor-args '-vcodec copy'"

alias yt="yt_audio -o '/downloads/%(title)s.%(ext)s'"
alias ytl="yt_audio -o '/downloads/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s'"

alias ytv="yt_video -o '/downloads/%(title)s.%(ext)s'"
alias ytv2k="yt_video2k -o '/downloads/%(title)s.%(ext)s'"
alias ytvl="yt_video -o '/downloads/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s'"
alias ytv2kl="yt_video2k -o '/downloads/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s'"
```

**2. Usage**

| Command | Description |
| --- | --- |
| yt_files | Open folder contain files downloaded |
| yt_log | Tail container log |
| yt | Download audio at best quality. |
| ytl | Download playlist audio at best quality. |
| ytv | Download video max resolution at 1080 quality |
| ytvl | Download playlist video max resolution at 1080 quality |
| ytv2k | Download video max resolution at 2K quality |
| ytvl2k | Download playlist video max resolution at 2K quality |

*Example:*
```
yt https://www.youtube.com/watch?v=Ex8IeflzftY
ytl https://www.youtube.com/watch?v=d--UOXdbtuE&list=PLbnOcTug_qTzM9-08LJddoirbpYX18zq0

ytv https://www.youtube.com/watch?v=Ex8IeflzftY
ytvl https://www.youtube.com/watch?v=d--UOXdbtuE&list=PLbnOcTug_qTzM9-08LJddoirbpYX18zq0

ytv2k https://www.youtube.com/watch?v=Ex8IeflzftY
ytvl2k https://www.youtube.com/watch?v=d--UOXdbtuE&list=PLbnOcTug_qTzM9-08LJddoirbpYX18zq0
```

### Environment variables

| Environment variable | Default | Description |
| --- | --- | --- |
| `LOG` | `yes` | Writes youtube-dl output to `/downloads/log.txt` or not |
| `AUTOUPDATE` | `no` | Updates youtube-dl to the latest version at launch |
| `GOTIFYURL` |  | Gotify server URL address (i.e. `http://192.168.1.5:8000` or `https://a.com/gotify`) |
| `GOTIFYTOKEN` |  | Gotify server Token |

### Downloads directory permission issues

You can either:

- Change the ownership and permissions of `./downloads` on your host with:

    ```sh
    chown 1000 -R ./downloads
    chmod 700 ./downloads
    chmod -R 600 ./downloads/*
    ```

- Launch the container with a different user using `--user=$UID:$GID`
