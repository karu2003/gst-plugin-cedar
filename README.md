# GStreamer 1.0 plugin for Cedar h264 hardware encoding with no binary blobs.

Based on PoC h264 encoder by Jens Kuske:
https://github.com/jemk/cedrus/tree/master/h264enc

Usage: tested KERNEL 6.2 on Banana Pi M2 Zero

gst-launch-1.0 videotestsrc ! cedar_h264enc ! h264parse ! matroskamux ! filesink location=cedar.mkv -e
gst-launch-1.0 -vv videotestsrc ! cedar_h264enc ! h264parse ! mp4mux ! filesink location=cedar.mp4 -e
gst-launch-1.0 -vv videotestsrc ! cedar_h264enc ! h264parse ! qtmux ! filesink location=cedar.mp4 -e

# Stream H264

# Server

media-ctl --device /dev/media0 --set-v4l2 '"ov5640 1-003c":0[fmt:UYVY8_2X8/1280x720@1/30]'

gst-launch-1.0 -v v4l2src device=/dev/video0 num-buffers=-1 ! video/x-raw,pixelformat=NV12,width=1280, height=720, framerate=30/1 ! cedar_h264enc ! rtph264pay config-interval=10 pt=96 ! udpsink host=192.168.3.174 port=5000

# Client

gst-launch-1.0 udpsrc port=5000 ! application/x-rtp,media=video,clock-rate=90000,encoding-name=H264,payload=96 ! rtph264depay ! decodebin ! videoconvert ! autovideosink sync=false


 

