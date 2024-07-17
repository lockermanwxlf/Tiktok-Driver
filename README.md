# Tiktok-Driver
A wrapper around [nodriver](https://github.com/ultrafunkamsterdam/nodriver) for getting posts from Tiktok. 
Also comes with a package for getting download urls for posts.

# Installation
```bash
pip install tiktok_driver
```

# Sample Usage
If don't want to write your own stuff you can go to [Tiktok-Scraper](https://github.com/lockermanwxlf/Tiktok-Scraper) which only 
requires you to write usernames in a csv and handles not downloading already downloaded posts for you.

```python
from tiktok_driver import TiktokDriver, TiktokErrors, DownloadType
from tiktok_driver.providers.tiksave import get_download_info
import requests

async def async_is_necessary_for_this():
    username = 'example'
    driver = await TiktokDriver.get_instance()
    result = await driver.get_user(username)
    posts = await result.get_posts()
    
    if len(posts) > 0:
        for post in posts:
            downloads = get_download_info(post.url)
            for i, download in enumerate(downloads, 1):
                filename = f'post {post.id}{f' {i}' if len(downloads) > 1 else ''}.{'png' if download.type == DownloadType.IMAGE else 'mp4'}'
                response = requests.get(download.url)
                response.raise_for_status()
                content = response.content
                with open(filename, 'wb+') as f:
                    content.write(content)
    else:
        try:
            await result.raise_for_error()
        except TiktokErrors.ProfileNonexistent:
            pass # stuff here
        except TiktokErrors.ProfilePrivate:
            pass # stuff here
```

# Contribution
Feel free to add more providers but they probably all target the same tiktok backend or cdn, so you'll probably only avoid rate limits from the download provider itself.
