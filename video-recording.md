# Record video/slideshow of running tests

## General considerations

* Limited to Firefox and Chrome, but supports both normal and headless mode. Taking plain images of these browsers in both modes already supported in the dedicated `chrome` and `firefox` browser providers by utilizing remote debugging protocols.
* A stream of captured images transformed encoded into video format by [FFMPEG](https://www.ffmpeg.org/).
* Should supports the following capturing modes:
  - All tests at once - because sometimes it's easier and convenient to upload one big file as an artifact to cloud storage provider (e.g. Amazon S3). Also, ffmpeg can output a stream to a remote server using RTMP protocol (used by streaming sites like YouTube, Twitch, etc.)
  - Test-by-test - you don't have rewind video for a some test, you can just open a file with the record of the test.
  - Only failed tests - minimal storage usage.
* [Strongly suggested] Support of patterns in recordings' paths like in https://github.com/DevExpress/testcafe/pull/2086
Patterns list:

| Pattern | Description |
| ------- | ----------- |
| `${FIXTURE}` | Fixture name |
| `${TEST}` | Test name |
| `${TEST_INDEX}` | Test index |
| `${FILE_INDEX}` | Numbers a file in NNN format for files of the same name in the same folder |
| `${DATE}` | Date the test was run (YYYY-MM-DD) |
| `${TIME}` | Time the test was run (HH-mm-ss) |
| `${DTF_<FORMAT>}` | Custom DateTime Format. Any supported MomentJS display format can be used here |
| `${USERAGENT}` | Concatenation of `${BROWSER}`, `${BROWSER_VERSION}`, `${OS}`, and `${OS_VERSION}` |
| `${BROWSER}` | Name of browser of current test |
| `${BROWSER_VERSION}` | Browser version of current test |
| `${OS}` | Name of operating system current test is currently run on |
| `${OS_VERSION}` | Version of operating system current test is currently run on |
| `${QUARANTINE_ATTEMPT}` | By default "1" (if you add it to the template but run tests without the `-q` option) |  

## CLI enhancements proposal

### --video-recording, -V <path>

Enables video recording of running tests.

#### Arguments:

* path - path to the video recording file or folder. 
  - if there are no patterns in path, specifies directory to save recordings for each useragent of a whole tests of of each test. Filename of a video recording of a 
  - if there are patterns in path, `${TEST}` or `${TEST_NAME}` patterns are required.

### --video-recording-options <options>

Have format of `key=value[,key2=value2][...]`. Key names should be named using camelCase.

#### List of options:

| key | description |
| --- | ----------- |
| `capturingMode` | Video capturing mode. Possible values: `all-tests-at-once`,`step-by-step`, `failed-only` |
| `encodingOptions` | String consisting of valid FFMPEG options | 

## API enhancements proposal

### Runner#videoRecording(path, options)

Enables video recording of running tests by using TestCafe JS API.

#### Arguments
* path - path to the video recording file or folder. Should be handled the same way described in the **CLI enhancements proposal** section.
* options - a plain JS object with `key:value` pairs, describing the options mentioned in the **CLI enhancements proposal** section. 
## Implementation details 
