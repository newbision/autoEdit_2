## Documentation Intro

If you are not familiar with [Node][node], [NWJS][nwjs], [backbone][backbone] or not sure were to start to get an overview to familiarise yourself with this project, check out the [prerequisite section](/jsdoc_docs/tutorial-prerequisites.html) to get an overview of the stack and see the minimum you need to know to get up to speed with this project.


Explore the documentation as [Docco style code documentation](/docco_docs/autoEdit2API.html), checkout the Github repo [for autoEdit2][autoEdit2Github] or continue reading below.

## Overview 

Follow these steps to get an overview of the workings of the app from the point of view of the main user journey.

### 1. User manual 
First things first, start by exploring the [user manual][usermanual] to get a sense of the main flow / user journey of the application.


### 2. IBM Blumix STT API keys
If you want to use it with IBM get the [bluemix STT keys](/user_manual/setup.html#setup)

### 4. Run the app 

```
npm install 
```

```
npm start 
```


### 5.Add IBM keys 

See [user manual for how to add them to the app on launch](https://opennewslabs.github.io/autoEdit_2/user_manual/setup.html#add-the-api-keys-to-the-app-at-startup).


### Follow the main user journey

[Follow user manual][usermanual] to  see the main user journey in getting a transcription from an audio/video file and a video sequence of selections.

- Add a file 
- Select some text 
- Export 

You can also try other export options: plain text, captions etc..

## Project folder structure.

```bash
.
├── LICENCE.md
├── README.md
├── appdmg.json
├── assets
│   ├── background.png
│   └── nw.icns
//binaries for ffmpeg and ffprobe 
├── bin
│   ├── ffmpeg
│   └── ffprobe
├── build
//build script to package the app
├── build.js
├── cache
├── config
│   ├── README.md
│   ├── build.js
│   ├── jsdoc_conf.json
│   └── make_demo.js
├── config.js
//project page jeckyll site + documentation, on github pages
├── docs
//project 
├── lib
//backbone app 
│   ├── app
│   ├── edl_composer
//this module is run in node context in nwjs
│   ├── interactive_transcription_generator
│   └── srt
├── node_modules
├── nwjs
//app.js is generated by browserify 
│   ├── app.js
│   ├── custom.css
//db.js is run in node context in nwjs
│   ├── db.js
//demo data for autoEdit online demo page
│   ├── demo_transcription.json
//starting point for nwjs is index.html 
│   ├── index.html
│   └── watson_keys.js
├── package.json
//tests
├── spec
//third party js compiled in browserify
└── vendor
    ├── backbone.async.js
    └── backbone.mousetrap.js

```


### `deploy.js`

Deployment script. more info on [packaging and building a new release here](/jsdoc_docs/tutorial-deployment.html).

### `docs` 

#### Project page
Contains a jeckyll site for the [project page, hosted on github pages](https://opennewslabs.github.io/autoEdit_2/).

Uses these two templates

- Landing page [pratt-app-landing-page](http://blacktie.co/2013/10/pratt-app-landing-page/) [demo](http://blacktie.co/demo/pratt)
- User manual: [jekyll-docs-template](https://github.com/bruth/jekyll-docs-template) [demo](http://bruth.github.io/jekyll-docs-template)

#### Documentation pages

##### user manual 

User manual as mentioned is a [jeckyll][jeckyll] besite and can be found inside `/docs` folder, the individual pages are markdown file inside `/docs/_posts`. 

##### Documentaiton
Using [jsdoc][jsdoc] and [docco][docco] for automatic documentation generated. (jsdoc is the one you are reading now). 

Developer documentation tutorials can be found in `/docs/_posts/tutorial`.

See [updating documentation](/jsdoc_docs/tutorial-updating_the_documentation.html) section for more on how to update [jsdoc][jsdoc] and [docco][docco]. 


### `spec`
Test suite `npm run test`.

### `nwjs`
is the front end of the project.

```
├── nwjs
	├── app.js
	├── custom.css
	├── db.js
	├── demo_transcription.json
	├── index.html
	└── watson_keys.js
```

`db.js` used in `lib/app/app.js` to override `backbone.sync` method to provide a backend for the app and persistent storage using linvodb3, which uses `medeadown` to storing db on the user file system. see [current db setup tutorial for more info](/jsdoc_docs/current_db_setup.html)


backbone front end . `demo_jr.js` is the code that runs the demo when index is runned in client side mode in the browser.

## `lib`
`app` contains the backbone project. this is packaged for the client side with browserify 

```
.
├── app
│   ├── app.js
│   ├── collections
│   │   └── transcriptions.js
│   ├── demo_db.js
│   ├── helpers.js
│   ├── models
│   │   └── transcription.js
│   ├── router.js
│   ├── templates
│   │   ├── 404.html.ejs
│   │   ├── home_page.html.ejs
│   │   ├── transcription_form_template.html.ejs
│   │   ├── transcription_index.html.ejs
│   │   ├── transcription_show.html.ejs
│   │   └── welcome.html.ejs
│   └── views
│       ├── transcription_form_view.js
│       ├── transcription_list_element_view.js
│       ├── transcription_list_view.js
│       ├── transcription_view.js
│       └── utils.js
├── edl_composer
│   ├── README.md
│   └── index.js
├── interactive_transcription_generator
│   ├── README.md
│   ├── index.js
│   ├── transcriber
│   │   ├── README.md
│   │   ├── convert_to_audio.js
│   │   ├── examples
│   │   ├── gentle_stt_node
│   │   ├── ibm_stt_node
│   │   ├── index.js
│   │   ├── split.js
│   │   └── trimmer.js
│   ├── video_metadata_reader
│   │   ├── README.md
│   │   ├── examples
│   │   └── index.js
│   └── video_to_html5_webm
│       └── index.js
└── srt
    └── index.js
```


### `interactive_transcription_generator`

After the user uploads a video or audio file the backbone app uses `autoEdit2API.js` to override default [`backbone.sync`][backbonesync].
 
- The app converts the file to an audio file meet the IBM Watson STT Specs
- if greater then 5 min long it splits it into 5 min chunk 
	- keeps tracks of the time offset of each clip 
- sends audio files to IBM STT API
- if submitted file was greater then 5 min
	- when results starts to come back as json after about 5 min or less results are re-interpolated into one json file
- json returned by IBM is converted into json meeting autoEdit2 specs and saved
- user can now view interactive transcription


As you might have guessed the module responsable for "post-processing" the audio and video file and get the transcription from IBM is called `interactive_transcription_generator`.

On top of prepping the audio or video file to get a transcription from IBM, it also generates a webm html5 video preview and reads the metadata, which is something needed make an [EDL][edl].


```
├── interactive_transcription_generator
	├── README.md
	├── bin
	│   ├── ffmpeg
	│   └── ffprobe
	├── index.js
	├── transcriber
	│   ├── README.md
	│   ├── convert_to_audio.js
	│   ├── examples
	│   ├── gentle_stt_node
	│   ├── ibm_stt_node
	│   ├── index.js
	│   ├── split.js
	│   └── trimmer.js
	├── video_metadata_reader
	│   ├── README.md
	│   ├── examples
	│   └── index.js
	└── video_to_html5_webm
	    └── index.js
```

----

Now that you have an high level overview, [feel free to jump into the code][autoEdit2Github].


[nwjs]: http://docs.nwjs.io/en/latest/For%20Users/Getting%20Started/
[node]:https://nodejs.org/en/
[backbone]:http://backbonejs.org/
[usermanual]:/user_manual/usage.html
[backbonesync]:http://backbonejs.org/#Sync
[edl]:/jsdoc_docs/tutorial-EDL_format.html
[autoEdit2Github]:https://github.com/OpenNewsLabs/autoEdit_2
[jeckyll]:https://jekyllrb.com/ 
[jsdoc]:http://usejsdoc.org/
[docco]:https://jashkenas.github.io/docco/