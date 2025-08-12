# payload_dumper
<!--
This was the **initial C prototype** of [payload-dumper-rust](https://github.com/rhythmcache/payload-dumper-rust). I originally started developing this tool in C but later migrated the project to Rust for better safety, maintainability, and performance.

This C version is now **completed for archival and comparison purposes**, but no longer actively maintained.
-->

## Dependencies

**Required:**
- liblzma
- libzstd  
- bzip2
- libbrotlidec
- libprotobuf-c

**Optional:**
- libcurl (for HTTP support)
- protoc-c (for regenerating protobuf files)

## Building

### Build

```bash
mkdir -p build && cd build
meson setup ..
ninja

# With HTTP support
mkdir -p build && cd build
meson setup .. -Denable_http=true
ninja
```

## Usage

```bash
Usage: ./payload_dumper <payload_source> [options]
Sources:
  <file_path>          Local payload.bin or ZIP file
  <http_url>           Remote ZIP file URL
Options:
  --out <dir>          Output directory (default: output)
  --images <list>      Comma-separated list of images to extract
  --list               List all partitions and exit
  --threads <num>      Number of threads to use
  --user-agent <ua>    Custom User-Agent for HTTP requests
  --help               Show this help message
```
<!--
## Current Status
**📦 Archived** - No longer actively maintained  
**🦀 Succeeded by** - [payload-dumper-rust](https://github.com/rhythmcache/payload-dumper-rust)
-->
