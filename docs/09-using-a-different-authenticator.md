# Using a Different Authenticator (GitHub Oauthenticator)

## Authenticators

Using GitHub OAuth - needs to use public accessible server but will require HTTPS. (We set up HTTPS above.)

### Set up GitHub Auth

```bash
sudo python3 -m pip install oauthenticator
```

Configure authenticator in `jupyterhub_config.py`:


GitHub set up of Oauth
https://github.com/settings/apps/new
- Developer settings
- Oauth App
    - Give a name jupyterhub the hard way
    - Homepage url https://hardway.jupyter.org
    - callback url https://hardway.jupyter.org/

Add Oauth info to `jupyterhub_config.py`


```bash
sudo apt install libssl-dev       libcurl4-openssl-dev
sudo python3 -m pip install pycurl
```
Note: above is a Tornado bug that seems to come up with GitHub Oauth.

