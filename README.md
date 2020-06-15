# Nextcloud Talk using Big Blue Button 

## -- Looking for a maintainer --

Since NextCloud opensourced their own signalling server we have migrated back to the original Talk. 
Our objective is in some sense fulfilled as we believe that open-sourcing was a direct result of our work.

However, if this repo is useful for you, consider becoming a maintainer

> This repo breaks with latest version (19) of NC

# -----

![](https://raw.githubusercontent.com/ramezrafla/spreed-bigbluebutton/stable18-bbb/docs/talkwithbbb.jpeg)

This is a fork of Nextcloud Talk to use [Big Blue Button](https://bigbluebutton.org/) as the video / audio conferencing server & client instead of Nextcloud's PHP-based signalling engine (or the Nextcloud paid cloud signaling servers)

## Rationale for the fork

Using PHP as a backend for video / audio conferencing is a bad idea for many reasons. It remains a good technical feat, but suffers from:
- Latency -- PHP does not maintain persistent connections with users and is an interpreted language
- Cannot maintain many connections at the same time (max is about 4-5 based on our own experience)
- Uses its own signaling protocol, so you have to either reinvent the wheel for a compiled native server or pay for the service
- There is no one going to maintain it besides the original developers. Something as complex as a video / audio conferencing requires a team -- future-proofing for enterprise deployments is paramount!

Paying for the third-party service by NC was not an option for us (maybe it is for you) -- it is too expensive for a solutions provider like us

## Why Big Blue Button

This is a tried-tested-and-true solution used by many educational institutions worldwide. It has been under active development for a very long time and is well-maintained. Uses Red5 and Kurento under the hood, and has a very nice and solid client (using our beloved [MeteorJS](https://www.meteor.com))

- A recommended BBB server can support over 100 simultaneous users
- BBB client can handle a lot more participants in the UI
- BBB client has drawing board, breakout rooms, uploading PDF,DOC,PPT,XLS
- BBB client has real-time notes
- BBB client UI is professional-grade (no offense intended for current Talk client UI)
- BBB uses WebRTC -- don't want to get sued like Zoom because they used their own protocol

## A note about mobile

Big Blue Button uses webrtc protocol which is supported by all recent browsers (even on iOS). Therefore, they do not have a mobile application for it.

> If you are using the Talk mobile app it won't work with this app. If a mobile app is a must you are stuck with the original Talk app.

## How to setup BBB

Super easy, they have an [automated install script](http://docs.bigbluebutton.org/2.2/install.html). You do need to have an SSL certificate setup (the install script does it for you, but you need to setup your domains properly). **Don't forget that you need to use Ubuntu 16**

# What we did

1. We load up an iframe with your BBB server where the old Talk client was, **only when in the Talk app**. The files details chat still uses the original signaling (which is easily capable of handling such a low-attendees call).

> Right now it is still internally called **spreed** which is the original name of the Talk App. The reasons are many, but prevent us from pushing into the NC Apps store. If you can help with this, please open an issue.

2. When a call is launched in the Talk app (not files sidebar) we hide both sidebars to have the call full width (you can reopen the call sidebar on the right by clicking on its icon)

3. We kept the chat as-is using the internal signaling server which is more than capable of handling chat traffic

4. We removed the Talk changelog that appears for new users (I understand the rationale, but annoying for system administrators)

> The original code is very well-written. Kudos to the original developers. Made it easier to make changes. We had to add a few helper functions here and there as some needs were not served well by existing ones (e.g. get the name of a guest to send to BBB)

# How to use this App

## First you have to manually clone it in your /apps folder

Of course, remove the original spreed

`git clone https://github.com/ramezrafla/spreed-bigbluebutton.git spreed --branch stable18-bbb --depth 1`

Notes: 
- Run that clone command in your /apps folder
- We included the build dir (/js) in this repo. In the future we will add releases (help wanted) or push in the Apps store (help wanted)
- Supports NC 18 only -- sorry, too much work porting back
- If you intend to develop omit the --depth argument

## Add your BBB server info in config/config.php

```
  'spreed' =>
  array (
    'bbb_server' => 'https://YOURSERVER/bigbluebutton/',
    'bbb_secret' => 'YOURSECRET',
  ),
```
or, alternatively create a config/custom.config.php: 
```
<?php
$CONFIG = <?php
$CONFIG = array ('spreed' =>
  array (
    'bbb_server' => 'https://YOURSERVER/bigbluebutton/',
    'bbb_secret' => 'YOURSECRET',
  ),
);
```

Notes:
- The trailing slash is important in the URL above
- You can get both info from your BBB server: `sudo bbb-conf --secret`
- We hope to add soon to the admin settings UI of this App the ability to edit these manually (help wanted)

## Test it

Reload your browser, clear your cache, etc. and you can now start calls with BBB

# Troubleshooting

1. Is the iframe loading when you start a call in the Talk app --> If not you are still using the old App
2. Make sure BBB server is working. Open your browser to `https://DOMAIN/bigbluebutton/` and `https://DOMAIN/bigbluebutton/api/`. You should get a response for both calls like this
```
<response>
   <script/>
   <returncode>SUCCESS</returncode>
   <version>2.0</version>
</response>
```
> **Anything else is a problem. Fix your BBB server first**
3. You are getting a blank iframe --> are you sure you properly setup `bbb_secret` and `bbb_server` parameters?
4. if you're getting a black iframe, check your browser console for cookie errors. Your BBB domain must be equal to the nextcloud domain: cloud.mydomain.de/bbb.mydomain.de work; cloud.mydomain.de/bbb.yourdomain.de might not work (same site cookie issue)

# TODO

Needless to say, help wanted

1. Add the 2 BBB parameters (server and key) in the admin settings UI of this App instead of config.php
2. Change name of this app -- recommended: name = 'Talk with BigBlueButton' and id = 'talk_bbb'
3. Publish to apps store under new name

If you need a BBB server for testing, please PM me.

# Please don't

1. Ask for help to setup your BBB server -- out of scope. If you are having issues read the docs or look for a third-party provider
2. Ask for help before you go through the troubleshooting steps above
3. Criticize this work needlessly -- it was done to serve a purpose (for example, we did not add function docs above the new functions, later ...)
4. Ask us to add your must-have features for you -- we are sharing this in the hopes that it is useful and welcome good PRs; so there is no reason you can't do it yourself or pay someone else

> ---- Original Talk README below this line ----

# Nextcloud Talk

**Video- & audio-conferencing app for Nextcloud**

![](https://raw.githubusercontent.com/nextcloud/spreed/master/docs/call-in-action.png)

## Why is this so awesome?

* 💬 **Chat integration!** Nextcloud Talk comes with a simple text chat. Allowing you to share files from your Nextcloud and mentioning other participants.
* 👥 **Private, group, public and password protected calls!** Just invite somebody, a whole group or send a public link to invite to a call.
* 💻 **Screen sharing!** Share your screen with participants of your call. You just need to use Firefox version 52 (or newer), latest Edge or Chrome 49 (or newer) with this [Chrome extension](https://chrome.google.com/webstore/detail/screensharing-for-nextclo/kepnpjhambipllfmgmbapncekcmabkol).
* 🚀 **Integration with other Nextcloud apps** like Files, Contacts and Deck. More to come.
* 🙈 **We’re not reinventing the wheel!** Based on the great [simpleWebRTC](https://simplewebrtc.com/) library.

And in the works for the [coming versions](https://github.com/nextcloud/spreed/milestones/):
* 🙋 [Federated calls](https://github.com/nextcloud/spreed/issues/21), to call people on other Nextclouds

If you have suggestions or problems, please [open an issue](https://github.com/nextcloud/spreed/issues) or contribute directly :)

### Supported Browsers

| Browser | Compatible |
|---|---|
| Firefox | ✔️ 52 or later |
| Chrome/Chromium | ✔️ 49 or later |
| Edge | ⚠️ latest versions <br> 🎤 Speakers are not promoted <br> 🏷 Name changes while a call is on-going are not reflected |
| Safari | ⚠️ 12 or later <br> ❌ No screensharing support <br> 🖥 Viewing screens of others works |


## Installing for Production

Nextcloud Talk is really easy to install. You just need to enable the app from the [Nextcloud App Store](https://apps.nextcloud.com/apps/spreed) and everything will work out of the box.

There are some scenarios (users behind strict firewalls / symmetric NATs) where a TURN server is needed. That's a bit more tricky installation, but the guys from [Nextcloud VM](https://github.com/nextcloud/vm) have developed a script which takes care of everything for you. You can find the script [here](https://github.com/nextcloud/vm/blob/master/apps/talk.sh). The script is tested on Ubuntu Server 18.04, but should work on 16.04 as well. Please keep in mind that it's developed for the VM specifically and any issues should be reported in that repo, not here.

Here's a short [video](https://youtu.be/KdTsWIy4eN0) on how it's done.

If you need to use Talk in a enterprise environment, including the ability to have calls with more than 5-6 users, you can contact our sales team for access to our [high performance back-end](https://nextcloud.com/talk/#pricing). This is a set of components that replaces some of the PHP code with a more scalable and performant solution that decreases network traffic and allows dozens or hundreds of users in a call.

## Development setup

1. Simply clone this repository into the `apps` folder of your Nextcloud development instance.
2. Run `make dev-setup` to install the dependencies;
3. Run `make build-js`
4. Then activate it through the apps management. :tada:
5. To build the docs locally, install mkdocs locally: `apt install mkdocs mkdocs-bootstrap`

We are also available on [our public Talk team conversation](https://cloud.nextcloud.com/call/c7fz9qpr), if you want to join the discussion.

### API documentation

The API documentation is available at https://nextcloud-talk.readthedocs.io/en/latest/

### Milestones and Branches

#### Branches

In the Talk app we have one branch per Nextcloud server version. stable* branches of the app should always work with the same branch of the Nextcloud server.
This is only off close to releases of the server, to allow easier finishing of features, so we don't have to backport them.

#### Milestones

* 5.0.0 - **Numeric** milestones are settled and waiting for their release or some final polishing
* 💙 Next Minor (15) - The **next minor** milestone is for issues/PR that go into the next Dot-Release for the given Nextcloud version (in the example 15 - e.g. 5.0.1)
* 💚 Next Major - The **next major** milestone is for issues/PR that go into the next feature release for the new Major Nextcloud version (as there are Minors for 15, this would be 16)
* 💛 Following Major - The **following major** milestone is for issues/PR that should be worked towards/on but didn't make it into the next major due to timing constraints
* 💔 Backlog - The **backlog** milestone is assigned to all remaining issues

You can always pick a task of any of the milestones and we will help you to get it into the assigned milestone or also an earlier one if time permits. It's just a matter of having an overview and better visibility what we think should be worked on, but it's not exclusive.


### Useful tricks for testing

* Disable camera until reboot: `sudo modprobe -r uvcvideo`
* Re-enable camera: `sudo modprobe uvcvideo`
* Send fake-stream (audio and video) in firefox:
  1. Open `about:config`
  2. Search for `fake`
  3. Toggle `media.navigator.streams.fake` to **true**


## Contribution Guidelines

For more information please see the [guidelines for contributing](https://github.com/nextcloud/spreed/blob/master/.github/contributing.md) to this repository.
