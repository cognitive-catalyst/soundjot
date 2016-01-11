

# SoundJot

Toolchain that helps convert a large library of videos and produce searchable 
transcripts, using Watson Text-to-Speech TTS. Output can be transformed, for
example to the raw ingestion format expected by Watson Multimedia Search (MMS).


## Modular Design
The modules below are listed in priority order, from critical to nice-to have.

### Core
Accept inputs and submits audio bytes to Watson Speech-to-Text, and records the resulting text or JSON response.
  - Options
  - Keywords

### Stream Getter
Accepts urls or file paths, and retrieves video as a binary stream.

### Session Helper
Intended to solve the problem of expired sessions in video urls.  Accepts a URL and returns a pair of attributes:

```
    { video_url: '', // either original url, or a base with session token removed
      refresh_url: ''; //where to get a new session, or null
      format: ['base','token','other'], // expected format of sessioned URL, or null
    }
```

### Downsampler
Accepts a video stream, downsamples to 16-bit FLAC, and outputs the results.

### Metadata Bundler
For a given video, collects a handful of metadata fields that are usually absent from transcript data.

###  Transform Modules
Accept one or more TTS outputs, metadata output, and converts to a desired output format. 

### Uploader
Submits finished transcripts to desired endpoint, e.g. Cloudant DB.

### Controller (Local)
? Gulp file that runs all the modules and pipes one to the next ?

### API Server
Exposes endpoints for transcription services. Ideally resides on a machine that handles all the stream data. Ideally pipes stream data instead of saving on disk.
Acts as a Controller module, and composer of other modules listed. It may take charge of looping over collections, and threading.

### GUI Server
Present a a page of controls that allows a user to submit videos or collections of videos.

### Crawler
Accepts a domain and path, and collects probably video source URLs from it.


## Workflow
Soundjot is intended to be semi-automated and will likely become more automatic with continued development.

1. Data entry 
  - stream url(s)
  - filepath(s)
    - [optional] Crawl and collect links
2. Stream Info (HEAD?)
3. Configuration - based on data entered and what is known about stream
4. TTS Auth - get token from Watson services
5. Get stream
6. Downsample stream
7. Send stream to TTS
8. Listen for result events and termination
9. Write JSON output (may be appends or one write operation)
10. Bundle metadata
11. Transform TTS transcript + metadata => desired output
12. Upload completed transcript
