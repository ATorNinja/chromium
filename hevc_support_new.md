## HEVC support ##

### For version ~67 and above ###

in the file:
- **src/third_party/ffmpeg/ffmpeg_generated.gni**

append to your condition:

```c
ffmpeg_c_sources += [
    "libavcodec/bswapdsp.c",
    "libavcodec/autorename_libavcodec_hevcdec.c",
    "libavcodec/hevc_cabac.c",
    "libavcodec/hevc_data.c",
    "libavcodec/hevc_filter.c",
    "libavcodec/hevc_mvs.c",
    "libavcodec/hevc_parse.c",
    "libavcodec/hevc_parser.c",
    "libavcodec/hevc_ps.c",
    "libavcodec/hevc_refs.c",
    "libavcodec/hevc_sei.c",
    "libavcodec/hevcdsp.c",
    "libavcodec/hevcpred.c",
    "libavcodec/x86/bswapdsp_init.c",
    "libavcodec/x86/hevcdsp_init.c",
    "libavformat/autorename_libavformat_hevc.c",
    "libavformat/hevcdec.c",
]
ffmpeg_yasm_sources += [
    "libavcodec/x86/bswapdsp.asm",
    "libavcodec/x86/hevc_deblock.asm",
    "libavcodec/x86/hevc_idct.asm",
    "libavcodec/x86/hevc_mc.asm",
    "libavcodec/x86/hevc_add_res.asm",
    "libavcodec/x86/hevc_sao.asm",
    "libavcodec/x86/hevc_sao_10bit.asm",
]
```
**then go to `src/third_party/ffmpeg` folder and copy:**
 - `libavcodec/hevcdec.c` to `libavcodec/autorename_libavcodec_hevcdec.c`
 - `libavformat/hevc.c` to `libavformat/autorename_libavformat_hevc.c`

in the files:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.asm**
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.h**

replace some parameters to:
```c
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```

in the file:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/libavcodec/codec_list.c**

add an option:
```c
&ff_hevc_decoder
```

in the file:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/libavcodec/parser_list.c**

add an option:
```c
&ff_hevc_parser
```

in the file:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/libavformat/demuxer_list.c**

add an option:
```c
&ff_hevc_demuxer
```

in the file:
- **src/media/base/mime_util_internal.cc**

above the line:
```c

if (video_codec != kUnknownVideoCodec) {
...
...
```

add the code:
```c
if (video_codec == kCodecHEVC) {
#if BUILDFLAG(ENABLE_HEVC_DEMUXING)
  return IsSupported;
#else
  return IsNotSupported;
#endif
}
```
