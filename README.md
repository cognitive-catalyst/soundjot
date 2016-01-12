
# SoundJot

Toolchain that helps convert a large library of videos and produce searchable 
transcripts, using [Watson Text-to-Speech](http://ibm.co/1PTfsY3) (TTS). 
Output can be transformed, for example to the raw ingestion format expected 
by Watson Multimedia Search (MMS).

## Usage
Soundjot is currently designed for fault tolerance, to work with varying
network conditions, media formats, etc. Toward that end, it writes files to 
local disk at each step. By default, these files will live in your user 
directory, with the following structure.

```
    $HOME/tmp-soundjot/
        ./in
        ./out
        
```

### Installation
Soundjot relies on [FFmpeg](https://ffmpeg.org/) to transcode media. 
[FFmpeg download options](https://ffmpeg.org/download.html) are described 
[here](https://ffmpeg.org/download.html). The fewest issues have been 
observed by installing via `git clone` and hand-tailoring the elaborate 
configuration options. A  bash file is included to illustrate options that 
have worked best.

```

    ./ffmpeg_install.bash
    npm install
    
```

### Operation
You may provide a path to a line-delimited file or a URL to the -f option. It 
must contain paths or URLs to video resources with dialog.

#### Simple Example

```

   node soundjot.js -f /path/to/streams.txt --CLOBBER=false
    
```

#### Command-Line Options

```

    Usage: soundjot node soundjot.js [options] -f <file>
    Options:
        -h, --help                     output usage information
        -V, --version                  output the version number
        -d, --dir <path>               Working directory
        -f, --inputfile <filename>     Specify input
        -o, --outputdir <path>         Specify destination directory
        -u, --username [string]        Watson service username
        -p, --password  [string]       Watson service password
        -s, --segment_time [hh:mm:ss]  Output chunk duration
        -x, --max_size [number]        Stop transcoding at max_size bytes
        -c, --contenttype [string]     Content-Type of input
        -t, --transform [path]         Transforms results to desired format
        -m, --metabundler [path]       Metadata bundler
        --uploader [path]              Uploader
        
```

## Workflow
Soundjot is intended to be semi-automated and will likely become more automatic 
with continued development.

1. Data entry 
  - stream url(s)
  - filepath(s)
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


### Underlying Services
To learn more about [Watson Text-to-Speech](http://ibm.co/1PTfsY3), please 
visit its [API documentation](http://ibm.co/1P50kU6). SDKs in popular languages
are also provided in the 
[Watson Developer Cloud](https://github.com/watson-developer-cloud). Soundjot
is built using the Developer Cloud [Node SDK](http://bit.ly/1ZhdWS3).



## Modular Design
The modules below are listed in priority order, from critical to nice-to have.

### Core
Accept inputs and submits audio bytes to Watson Speech-to-Text, and records the 
resulting text or JSON response.
  - Options
  - Keywords

### Stream Getter / Downsampler
Accepts urls or file paths, and retrieves video as a binary stream.
Downsamples the video stream to to 16-bit FLAC, and outputs the results.

### Metadata Bundler
For a given video, collects a handful of metadata fields that are usually absent
from transcript data.

###  Transform Modules
Accept one or more TTS outputs, metadata output, and converts to a desired 
output format. 

### Uploader
Submits finished transcripts to desired endpoint, e.g. Cloudant DB.

### Controller (Local)
Runs all the modules in stepwise fashion.


## Proposed Enhancements

### API Server
Exposes endpoints for transcription services. Ideally resides on a machine that 
handles all the stream data. Ideally pipes stream data instead of saving on 
disk. Acts as a Controller module, and composer of other modules listed. It 
may take charge of looping over collections, and threading.

### GUI
Present a a page of controls that allows a user to submit videos or collections
of videos.

### Crawler
Accepts a domain and path, and collects probably video source URLs from it.


