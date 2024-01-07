# Fluentd logger with Python


<details>

## <summary>Table of Contents</summary>

- [Fluentd](#fluentd)
    - [Installation](#installing)
    - [Status](#status)
    - [Configuration](#config)
- [Python](#python)
- [License](#license)

</details>

<a name="fluentd"></a>
## Fluentd
<a name="installing"></a>
### Installing
```commandline
curl -fsSL https://toolbelt.treasuredata.com/sh/install-debian-bookworm-fluent-package5-lts.sh | sh
```
<a name="status"></a>
### Status (Linux)
```commandline
systemctl start fluentd.service
systemctl status fluentd.service
```
<a name="config"></a>
### Configuring
```
vim /etc/fluent/fluentd.conf

# Capture logs from Python script
<source>
    @type forward
    port 24225
</source>

# Ship the logs to file
<match fluentd.test.**>
    @type file
    path /var/log/fluent/test.log
</match>
```
<a name="python"></a>
## Python
### Installing PIP
```commandline
apt install python3-pip
```
### Install plugins
```commandline
pip3 install fluent-logger requests
```
### Example code
```python
from fluent import sender
from fluent import event

# Fluentd setup
sender.setup('fluentd.test', host='localhost', port=24225)

import requests

query = 'python'

url = 'https://en.wikipedia.org/w/api.php'
params = {
    'action':'query',
    'format':'json',
    'list':'search',
    'utf8':1,
    'srsearch':query
}

data = requests.get(url, params=params).json()
titles = [result['title'] for result in data['query']['search']]

# Counting the number of titles
num_titles = len(titles)
print(f'Number of titles: {num_titles}')


# Log the article count using Fluentd
event.Event('wikipedia_article_count', {
    'titles_count': num_titles
})

```

## License

[MIT](https://choosealicense.com/licenses/mit/)
