# Szurubooru VR
This repository has everything necessary to add a VR player to your Szurubooru instance. Any device/browser that supports WebXR (pretty much every standalone VR headset and smartphone) is compatible.

A new web server (powered by Nginx) gets created within the Szurubooru docker stack which hosts the VR player (powered by A-Frame). This new web site grabs the file path and streams it in the VR player, without download or upload.

Whenever you tag a post in Szurubooru with 'vr' (or whichever tag you define) a new button appears that leads you to the VR player.

## Requirement
You need to install [Szurubooru](https://github.com/rr-/szurubooru). Please follow the installation instructions. Make sure you build the client image. Everything is detailed on the Szurubooru GitHub page.

Existing Szurubooru instances also work. You only need to modify your _docker-compose.yaml_ file and build the images. I do not supply a full _docker-compose.yaml_ file, only the part that is necessary for the VR player.

HTTPS is required for the VR player to work. This means you have an SSL certificate (either self signed or from ex. Let's Encrypt). Since the VR player is being served by Nginx you can modify the nginx.conf file to listen to HTTPS. However I recommend using a seperate reverse proxy (as Szurubooru also recommends).

## Installation
### docker-compose.yaml
Modify the Szurubooru _docker-compose.yaml_ and add the vr block from my yaml file. Make sure your networks are correct. The VR player needs to communicate to your Szurubooru instance as well as your reverse proxy (if it is being used).
### Directory
Copy the vrplayer directory in your Szurubooru root directory (where the client and server directory are).

```
.
├── client
├── server
├── vrplayer
│   ├── nginx.conf
│   ├── vrplayer.html
├── docker-compose.yaml
├── ...
```
Check _nginx.conf_ and make sure the line `proxy_pass http://szuru-client-1:80/data/posts/;`  is correctly pointing at your Szurubooru client container. If you have left your Szurubooru installation as is then it should be correct.
### post_readonly_sidebar.tpl
Copy the file _post_readonly_sidebar.tpl_ into _./client/html_ and replace/rename the old file. At line 64 of the new file you have to do some modifications:
```
if (tagObj && Array.isArray(tagObj._names) && tagObj._names.indexOf('vr') !== -1) {
```
Either change 'vr' or keep it. This is the tag that enables the VR play button.

Next at line 82 change the following line:
```
'http://vr-player-url/vrplayer.html?src=<%- encodeURIComponent(ctx.post.contentUrl) %>',
```
This is the URL to your VR player. Change the domain to the one you are using. This domain is the Nginx web server serving the VR player.

After all the files have been copied you can now build the client image with `docker compose build client` and spin up your Szurubooru instance with `docker compose up -d`

If everything is done correctly you should see a 'Play in VR' button on the posts that have the 'vr' tag. Pressing the button should lead you to your VR player from which you can start watching the video in VR. If you are modifying an existing Szurubooru instance then clear your cache as sometimes the button won't appear even if everything is done correctly.
# Disclaimer
This repository does NOT include Szurubooru.

It contains only:
- A modified template file
- Nginx configuration file
- A standalone VR player HTML file

## AI usage
For the sake of transparency I wish to inform that a vast majority of the code was created with the help of LLMs. I have done my utmost to test everything before uploading the repository. I employed these tools as a way for me to create a VR player for myself, as I am but a simple gooner and not a developer.
