# Elastic Transcoder

A media transcoder in the cloud

A way to *convert media files* from their original source format into different formats that will play on smartphones, tablets, PCs, etc.

A way of transcoding video.

Has *presets for popular output formats*, meaning you don't need to guess about which settings work best on particular devices.

## Fees

Pay based on

* the minutes you transcode
* The resolution at which you transcode

## Example

* Upload your video to S3
* A lambda function is triggered
* Lambda function looks at all the metadata and sends the video over to Elastic transcoder
* Transcoder looks at the video and transcodes it to look nice on a wide variety of devices.
* Transcoder ships the transcoded videos to another S3 bucket