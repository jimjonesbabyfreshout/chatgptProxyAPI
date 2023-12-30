*After testing, the following figure will be displayed when some ip accesses*

<img src="https://image.3001.net/images/20231116/1700131811_6555f3e30a1efc2db761f.png" height="50%" width="50%" /></a>


---
### Demo

[https://chatai.451024.xyz](https://chatai.451024.xyz)

> api

```url
https://openai.451024.xyz
```


```url
https://openai-proxy-api.pages.dev/api
```
***

> New project [WeChat robot based on OpenAI] (https://github.com/x-dr/wechat-bot )

***

### The demo station is a public service. If you need to use it on a large scale, please deploy it yourself. The demo station is a bit overwhelmed.

![worker](./docs/img/worker.png)


## Use Cloudflare Worker中转api.openai.com

1. Create a new Cloudflare worker
2. copy [cf_worker.js ](https://cdn.jsdelivr.net/gh/x-dr/chatgptProxyAPI@main/cf_worker.js ) Paste the code in the worker and deploy it
3. Bind a domain name that is not blocked to the worker
4. Use your own domain name instead api.openai.com


**[Detailed tutorial] (./docs/cloudflare_workers.md)**



***



## Use CloudFlare Pages for transit

###1. Deploy transit API + Openai API balance query (use sess-xxxx Authorization query, the effective time is unknown)

> [Official documentation] (https://developers.cloudflare.com/pages )

1. ~~Fork this project~~Click [Use this template] (https://github.com/x-dr/chatgptProxyAPI/generate ) The button creates a new code base.
2. Log in to [Cloudflare] (https://dash.cloudflare.com /) Console.
3. In the account homepage, select `pages'>'Create a project'>'Connect to Git`
4. Select the project repository you fork, and in the'set up builds and deployments` section, all defaults.


5. Click'save and Deploy'to deploy, and then click 'Continue to project'to see the access domain name


> Put the official interface`https://api.openai.com 'Replace with`https://xxx.pages.dev 'That's it

**Demo**

[https://chatai.451024.xyz](https://chatai.451024.xyz)

**[Detailed tutorial] (./docs/cloudflare_pages.md)**


###2. Only deploy the transit API


**[Detailed tutorial] (./docs/cloudflare_proxy_pages.md)**




## docker deployment (requires overseas vps) 

> It seems that sse is not supported, so it is not recommended

<details>

<summary>e.g.</summary>

```bash
docker run -itd --name openaiproxy \
            -p 3000:3000 \
            --restart=always \
           gindex/openaiproxy:latest
```

#### Use

*api : http://vpsip:3000/proxy/v1/chat/completions*

```bash
curl --location 'http://vpsip:3000/proxy/v1/chat/completions' \
--header 'Authorization: Bearer sk-xxxxxxxxxxxxxxx' \
--header 'Content-Type: application/json' \
--data '{
   "model": "gpt-3.5-turbo",
  "messages": [{"role": "user", "content": "Hello!"}]
 }'

```

</details>



## Usage


<details>

<summary>fetch for JavaScript</summary>

```javascript
const requestOptions = {
    method: 'POST',
    headers: {
        "Authorization": "Bearer sk-xxxxxxxxxxxx",
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        "model": "gpt-3.5-turbo",
        "messages": [
            {
                "role": "user",
                "content": "hello word"
            }
        ]
    })
};

fetch("https://openai.1rmb.tk/v1/chat/completions", requestOptions)
    .then(response => response.text())
    .then(result => console.log(result))
    .catch(error => console.log('error', error));
  
```

</details>



<details>

<summary> Use python </summary>

```python
import requests

url = "https://openai.1rmb.tk/v1/chat/completions"
api_key = 'sk-xxxxxxxxxxxxxxxxxxxx'

headers = {
  'Authorization': f'Bearer {api_key}',
  'Content-Type': 'application/json'
}

payload = {
  "model": "gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "hello word"
    }
  ]
}

try:
    response = requests.post(url, headers=headers, json=payload)
    response.raise_for_status() # Throws an exception if the response code is not 200
    data = response.json()
    print(data)
except requests.exceptions.RequestException as e:
    print(f"Request error: {e}")
except json.JSONDecodeError as e:
    print(f"invalid JSON response: {e}")
```

</details>



<details>
<summary> Use nodejs chatgpt library </summary>

[transitive-bullshit/chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

```javascript
import { ChatGPTAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTAPI({
    apiKey: "sk-xxxxxxxxxxxxxx",
  // proxy+/v1
    apiBaseUrl:"https://openai.1rmb.tk/v1"


  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}

example()

```

</details>



<details>

<summary>Check balance </summary>

```javascript
    const headers = {
      'content-type': 'application/json',
      'Authorization': `Bearer sk-xxxxxxxxxxxxxxxxx`
    }
    // Check whether to subscribe
    const subscription = await fetch("https://openai.1rmb.tk/v1/dashboard/billing/subscription", {
      method: 'get',
      headers: headers
    })
    if (!subscription.ok) {
      const data = await subscription.json()
      // console.log(data);
      return data
      // throw new Error('API request failed')
    } else {
      const subscriptionData = await subscription.json()
      const endDate = subscriptionData.access_until
      const startDate = new Date(endDate - 90 * 24 * 60 * 60);
      console.log(formatDate(endDate, "YYYY-MM-DD"));
      console.log(formatDate(startDate, "YYYY-MM-DD"));
      const response = await fetch(`https://openai.1rmb.tk/v1/dashboard/billing/usage?start_date=${formatDate(startDate, "YYYY-MM-DD")}&end_date=${formatDate(endDate, "YYYY-MM-DD")}`, {
        method: 'get',
        headers: headers
      })
      
      const usageData = await response.json();
      // Account type
      const plan = subscriptionData.plan.id
      console.log(usageData);
      }

```

</details>

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=x-dr/chatgptProxyAPI&type=Date)](https://star-history.com/#x-dr/chatgptProxyAPI&Date)
