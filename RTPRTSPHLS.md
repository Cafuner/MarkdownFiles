rtsp

Provider vlc: media->stream->add file->stream->next->rtsp add->port=8554,path=/rtsp001->next->next->stream

Consumer vlc: open source-> rtsp:"provider ip":8554/rtsp001

