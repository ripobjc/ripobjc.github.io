---
layout: post
title:  "Sample Rate Conversion using AVFoundation"
categories: en
tags: objective-c AVFoundation audio
---

```
{% highlight objc %}
AVAudioFile *file;
AVAudioFormat *internalFormat;
AVAudioPCMBuffer *audioFileBuffer;
NSError *err = nil;

file = [[AVAudioFile alloc] initForReading:url error:&err];

internalFormat = [[AVAudioFormat alloc] initWithCommonFormat:AVAudioPCMFormatFloat32
                                                  sampleRate:44100
                                                    channels:2
                                                 interleaved:NO];

audioFileBuffer = [[AVAudioPCMBuffer alloc] initWithPCMFormat:file.fileFormat
                                                frameCapacity:(AVAudioFrameCount)[file length]];

[file readIntoBuffer:audioFileBuffer
               error:&err];

if (audioFileBuffer.format.sampleRate != internalFormat.sampleRate) {
    
    AVAudioConverter *converter;
    AVAudioPCMBuffer *convertedAudioBuffer;
    NSError *err = nil;

    int new_frame_length = ceil((double)[file length] / audioFileBuffer.format.sampleRate * internalFormat.sampleRate);

    convertedAudioBuffer = [[AVAudioPCMBuffer alloc] initWithPCMFormat:internalFormat
                                                         frameCapacity:new_frame_length];
    converter = [[AVAudioConverter alloc] initFromFormat:audioFileBuffer.format
                                                toFormat:convertedAudioBuffer.format];
    
    converter.sampleRateConverterQuality = AVAudioQualityMax;
    converter.sampleRateConverterAlgorithm = AVSampleRateConverterAlgorithm_Normal;
    // or AVSampleRateConverterAlgorithm_Mastering or AVSampleRateConverterAlgorithm_MinimumPhase

    __block int counter = 0;
    [converter convertToBuffer:convertedAudioBuffer
                         error:&err
            withInputFromBlock:^AVAudioBuffer * _Nullable(AVAudioPacketCount inNumberOfPackets,
                                                          AVAudioConverterInputStatus * _Nonnull outStatus)
        {
            int max_processing_frames = 8192 * 4;
            int n_processing_frame = MIN(MIN(inNumberOfPackets, max_processing_frames) , audioFileBuffer.frameLength-counter);
            AVAudioPCMBuffer *tmpBuffer = [[AVAudioPCMBuffer alloc] initWithPCMFormat:convertedAudioBuffer.format
                                                                        frameCapacity:n_processing_frame];

            if (n_processing_frame == 0)
                *outStatus = AVAudioConverterInputStatus_EndOfStream;
            else {
                *outStatus = AVAudioConverterInputStatus_HaveData;
                NSLog(@"convertToBuffer: %d", (int)n_processing_frame);
                for (int ch=0;ch<2;ch++) {
                    memcpy(tmpBuffer.mutableAudioBufferList->mBuffers[ch].mData,
                            ((Float32*)(audioFileBuffer.audioBufferList->mBuffers[ch].mData)) + counter,
                            n_processing_frame * sizeof(Float32));
                }
                tmpBuffer.frameLength += n_processing_frame;
                counter += n_processing_frame;
            }
            return tmpBuffer;
        }
    ];
    assert(err == nil);
    audioFileBuffer = convertedAudioBuffer;
}
{% endhighlight %}
```


Note the following AVAudioConverter's simpler synchronous methond does *not* convert the sample rate.
```
{% highlight objc %}
[converter convertToBuffer:convertedAudioBuffer
               fromBuffer:audioFileBuffer
                    error:&err];
{% endhighlight %}
```
