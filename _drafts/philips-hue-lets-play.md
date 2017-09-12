---
layout: post
...

# Philips Hue API for Python

Before we begin, take note that there will be code examples
which assume you'll be using Jupyter Notebook.
If you're not, here's a quick workaround:

```python
display = print
```

## Getting Started

Let's give this a try.
Taking a look at the [Getting started] document on [developers.meethue.com],
I see that the first task is getting on the same network as the Hue hub
and confirming its connection and ours.
Since I've confirmed my hub is already connected
and I can control lights with my phone,
I can skip the first step in [Getting started].


[Getting started]: https://developers.meethue.com/documentation/getting-started
[developers.meethue.com]: https://developers.meethue.com

### Finding the Philips hue bridge

My choices are:

> 1. Use a UPnP discovery app to find Philips hue in your network.
> 2. Use our broker server discover process by visiting www.meethue.com/api/nupnp
> 3. Log into your wireless router and look Philips hue up in the DHCP table.
> 4. Hue App method: Download the official Philips hue app. Connect your phone to the network the hue bridge is on. Start the hue app(iOS described here). Push link connect to the bridge. Use the app to find the bridge and try controlling lights. All working -- Go to the settings menu in the app. Go to My Bridge. Go to Network settings. Switch off the DHCP toggle. The ip address of the bridge will show. Note the ip address, then switch DHCP back on

Since I want to do this automatically in the future,
I'll opt for using the broker server (option 2).


```python
"""Use the broker server at meethue.com to locate any Philips hue bridges on the local network."""

URL_FIND_BRIDGE_NUPNP = 'https://www.meethue.com/api/nupnp'  # https://developers.meethue.com/documentation/hue-bridge-discovery

import urllib.request
```

I'll be getting JSON back, but `urllib` doesn't deal with that,
so let me import the `json` module and wrap `urlopen` in `json.load`,
for my convenience.


```python
import json

def request_json(*args):
    """Returns parsed JSON from server"""
    return json.load(urllib.request.urlopen(*args))
```

For the sake of readability, I'd rather have a well named function
so we don't have to think about URLs and serialization formats.


```python
def request_bridges():
    return request_json(URL_FIND_BRIDGE_NUPNP)
```

Now I can call `request_bridges()` to get a list:


```python
my_bridges = request_bridges()
```

My bridge is the first bridge, 'cause I have only one.


```python
my_bridge = my_bridges[0]
```

Let's take a look at that.


```python
display(my_bridge)  # (display can be mapped to print, if you're not using Jupyter)
```


    {'id': '001788fffe24d54e', 'internalipaddress': '10.0.1.4'}


I can see the IP address as the `internalipaddress` attribute.

I can do things with it, such as use it in strings.


```python
display(f'My bridge is at {my_bridge["internalipaddress"]}.')
```


    'My bridge is at 10.0.1.4.'


If the IP address represents a hub, we can confirm it's online and connected
by calling the `config` API, as suggested in [Hue bridge discovery].

[Hue bridge discovery]: https://developers.meethue.com/documentation/hue-bridge-discovery

If we'll be using lots of URLs, we should make a quick template to avoid repetition.


```python
api_url = lambda path: f'http://{my_bridge["internalipaddress"]}{path}'
```

I'm going to go a little further and assume it might be handy
to keep a copy of the data we're about to retrieve.
I'll store the data as an attribute in `my_bridge`.


```python
my_bridge[api_config_path] = request_json(api_url('/api/config'))
```

Let's see how that turned out:


```python
display(my_bridge)
```


    {'/api/config': {'apiversion': '1.20.0',
      'bridgeid': '001788FFFE24D54E',
      'datastoreversion': '63',
      'factorynew': False,
      'mac': '00:17:88:24:d5:4e',
      'modelid': 'BSB002',
      'name': 'RingoTree',
      'replacesbridgeid': None,
      'starterkitid': '',
      'swversion': '1707040932'},
     'id': '001788fffe24d54e',
     'internalipaddress': '10.0.1.4'}


Great! Let's continue to [Create user] in the [Philips hue API].

[Create user]: https://developers.meethue.com/documentation/configuration-api#71_create_user
[Philips hue API]: https://developers.meethue.com/philips-hue-api

---

### Create user

```
POST /api
```

> Creates a new user. The link button on the bridge must be pressed and this command executed within 30 seconds.

And what should we expect as a response?

> Contains a list with a single item that details whether the user was added successfully along with the username parameter.  If successful the username should be stored for future API calls.

A list. So, we can use our `request_json` function for this.


```python
def create_user():
    return request_json(api_url('/api'), bytes(json.dumps(dict(devicetype='jupyter-notebook#DevvynPM')), encoding="utf-8"))
```


```python
create_user()
```




    [{'error': {'address': '',
       'description': 'link button not pressed',
       'type': 101}}]



> The link button on the bridge must have been recently pressed for the command to execute successfully. If the link button has not been pressed a 101 error will be returned.


```python
def get_me():
    try:
        response = create_user()
        yield from (user['success'] for user in response)
    except KeyError as e:
        raise Exception(response)
```


```python
display(list(get_me()))
```


    ---------------------------------------------------------------------------

    KeyError                                  Traceback (most recent call last)

    <ipython-input-64-a0ef0c68afb1> in get_me()
          3         response = create_user()
    ----> 4         yield from (user['success'] for user in response)
          5     except KeyError as e:


    <ipython-input-64-a0ef0c68afb1> in <genexpr>(.0)
          3         response = create_user()
    ----> 4         yield from (user['success'] for user in response)
          5     except KeyError as e:


    KeyError: 'success'

    
    During handling of the above exception, another exception occurred:


    Exception                                 Traceback (most recent call last)

    <ipython-input-65-ca2d7d19cfa3> in <module>()
    ----> 1 display(list(get_me()))
    

    <ipython-input-64-a0ef0c68afb1> in get_me()
          4         yield from (user['success'] for user in response)
          5     except KeyError as e:
    ----> 6         raise Exception(response)
    

    Exception: [{'error': {'type': 101, 'address': '', 'description': 'link button not pressed'}}]


---

I've pressed the button. I'll try again.


```python
me = next(get_me())
```


```python
display(me)
```


    {'username': 'cV4mAIgcdlY4vgXfpCjlk0eS9gqvYTThwZfugxQO'}


Splendid! Can I turn my light strip orange? We'll need to find a way to identify it. Let's get list of lights, and a basic filter search, by name.


```python
class Lights(object):
    def __init__(self, username):
        self.url = api_url(f'/api/{username}/lights')
    
    def get_all(self):
        return request_json(self.url)
    
    @property
    def lights(self):
        return self.get_all()

    def get_by_name(self, search_string):
        return {key: light for key, light in self.lights.items() if search_string in light['name']}
```


```python
lights = Lights(me['username'])
search_results = lights.get_by_name('Light strip')
display(search_results)
```


    {'6': {'manufacturername': 'Philips',
      'modelid': 'LST002',
      'name': 'Light strip',
      'state': {'alert': 'none',
       'bri': 108,
       'colormode': 'xy',
       'ct': 500,
       'effect': 'none',
       'hue': 5999,
       'on': True,
       'reachable': True,
       'sat': 239,
       'xy': [0.5581, 0.3987]},
      'swupdate': {'lastinstall': None, 'state': 'noupdates'},
      'swversion': '5.50.2.19072',
      'type': 'Extended color light',
      'uniqueid': '00:17:88:01:01:1c:fa:7a-0b'}}


It feels like time to make a `Light` class, but let's revisit that after doing a quick test.


```python
request_json(api_url(f'/api/{me["username"]}/lights/6'))
```




    {'manufacturername': 'Philips',
     'modelid': 'LST002',
     'name': 'Light strip',
     'state': {'alert': 'none',
      'bri': 108,
      'colormode': 'xy',
      'ct': 500,
      'effect': 'none',
      'hue': 5999,
      'on': True,
      'reachable': True,
      'sat': 239,
      'xy': [0.5581, 0.3987]},
     'swupdate': {'lastinstall': None, 'state': 'noupdates'},
     'swversion': '5.50.2.19072',
     'type': 'Extended color light',
     'uniqueid': '00:17:88:01:01:1c:fa:7a-0b'}



Ugh, it looks like `urllib.request.urlopen` doesn't support `PUT` requests. I'll just do it by hand. At least we still have the `api_url` function.


```python
data = bytes(json.dumps(dict(on=True, bri=255, hue=int(65535*0.09))), encoding='utf-8')
request = urllib.request.Request(api_url(f'/api/{me["username"]}/lights/6/state'), data=data, method='PUT')
response = urllib.request.urlopen(request)
display(json.loads(response.read().decode('utf-8')))
```


    [{'success': {'/lights/6/state/on': True}},
     {'success': {'/lights/6/state/hue': 5898}},
     {'success': {'/lights/6/state/bri': 254}}]


Well, it wasn't very easy to get the right colour, and we couldn't re-use the `request_json` function, but I saw the light turn on and it looked orange to me. Success?

## Mixed results

### The good

The question was, _can I turn my light strip orange?_ The light reacted to a REST command, turning orange! The question has been answered.

### The bad

We ended up with a function that isn't as useful as we expected, as it can only "post" and read, but not "put".

### Next steps

It's probably time to get serious about re-usability. Nobody wants to have to re-use snippets of code for simple actions. Ultimately, we'll want a REST request method that's useful for all HTTP verbs, and it should also encode our arguments automatically. Once we go beyond individual lights, we'll probably find it useful to have shorthand methods for everything relevant. 

What would be relevant for our intended needs depends entirely on what we want to do. We should formalize our intentions, now that we know Jupyter Notebook is a decent place to tinker. A good idea might be to create an interactive dashboard. It may be helpful to see if any libraries have already been written to interface with Philips hue in Python, so we don't have te re-build classes as we go through exercises in this narrative fashion.

We also don't want our "username" lingering in the open in our examples, as this is an access token and it's considered security faux pas to prioritize convenience over secrecy when handling private access tokens. It's probably prudent to find a way of passing that token invisibly through our environment, or encrypting it at the very least. Environment variables are usually a good way to pass strings without storing them in distributed sources.
