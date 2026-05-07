# Go by Example

## Sadržaj

- [01 Hello World][01]
- [02 Values][02]
- [03 Variables][03]
- [04 Constants][04]
- [05 For][05]
- [06 If/Else][06]
- [07 Switch][07]
- [08 Arrays][08]
- [09 Slices][09]
- [10 Maps][10]
- [11 Functions][11]
- [12 Multiple Return Values][12]
- [13 Variadic Functions][13]
- [14 Closures][14]
- [15 Recursion][15]
- [16 Range over Built-in Types][16]
- [17 Pointers][17]
- [18 Strings and Runes][18]
- [19 Structs][19]
- [20 Methods][20]
- [21 Interfaces][21]
- [22 Enums][22]
- [23 Struct Embedding][23]
- [24 Generics][24]
- [25 Range over Iterators][25]
- [26 Errors][26]
- [27 Custom Errors][27]
- [28 Goroutines][28]
- [29 Channels][29]
- [30 Channel Buffering][30]
- [31 Channel Synchronization][31]
- [32 Channel Directions][32]
- [33 Select][33]
- [34 Timeouts][34]
- [35 Non-Blocking Channel Operations][35]
- [36 Closing Channels][36]
- [37 Range over Channels][37]
- [38 Timers][38]
- [39 Tickers][39]
- [40 Worker Pools][40]
- [41 WaitGroups][41]
- [42 Rate Limiting][42]
- [43 Atomic Counters][43]
- [44 Mutexes][44]
- [45 Stateful Goroutines][45]
- [46 Sorting][46]
- [47 Sorting by Functions][47]
- [48 Panic][48]
- [49 Defer][49]
- [50 Recover][50]
- [51 String Functions][51]
- [52 String Formatting][52]
- [53 Text Templates][53]
- [54 Regular Expressions][54]
- [55 JSON][55]
- [56 XML][56]
- [57 Time][57]
- [58 Epoch][58]
- [59 Time Formatting / Parsing][59]
- [60 Random Numbers][60]
- [61 Number Parsing][61]
- [62 URL Parsing][62]
- [63 SHA256 Hashes][63]
- [64 Base64 Encoding][64]
- [65 Reading Files][65]
- [66 Writing Files][66]
- [67 Line Filters][67]
- [68 File Paths][68]
- [69 Directories][69]
- [70 Temporary Files and Directories][70]
- [71 Embed Directive][71]
- [72 Testing and Benchmarking][72]
- [73 Command-Line Arguments][73]
- [74 Command-Line Flags][74]
- [75 Command-Line Subcommands][75]
- [76 Environment Variables][76]
- [77 Logging][77]
- [78 HTTP Client][78]
- [79 HTTP Server][79]
- [80 TCP Server][80]
- [81 Context][81]
- [82 Spawning Processes][82]
- [83 Exec'ing Processes][83]
- [84 Signals][84]
- [85 Exit][85]

[01]: 01_gbe.md#01-hello-world
[02]: 01_gbe.md#02-values
[03]: 01_gbe.md#03-variables
[04]: 01_gbe.md#04-constants
[05]: 01_gbe.md#05-for
[06]: 01_gbe.md#06-ifelse
[07]: 01_gbe.md#07-switch
[08]: 01_gbe.md#08-arrays
[09]: 01_gbe.md#09-slices
[10]: 01_gbe.md#10-maps
[11]: 01_gbe.md#11-functions
[12]: 01_gbe.md#12-multiple-return-values
[13]: 01_gbe.md#13-variadic-functions
[14]: 01_gbe.md#14-closures
[15]: 01_gbe.md#15-recursion
[16]: 01_gbe.md#16-range-over-built-in-types
[17]: 01_gbe.md#17-pointers
[18]: 01_gbe.md#18-strings-and-runes
[19]: 01_gbe.md#19-structs
[20]: 01_gbe.md#20-methods
[21]: 01_gbe.md#21-interfaces
[22]: 01_gbe.md#22-enums
[23]: 01_gbe.md#23-struct-embedding
[24]: 01_gbe.md#24-generics
[25]: 01_gbe.md#25-range-over-iterators
[26]: 02_gbe.md#26-errors
[27]: 02_gbe.md#27-custom-errors
[28]: 02_gbe.md#28-goroutines
[29]: 02_gbe.md#29-channels
[30]: 02_gbe.md#30-channel-buffering
[31]: 02_gbe.md#31-channel-synchronization
[32]: 02_gbe.md#32-channel-directions
[33]: 02_gbe.md#33-select
[34]: 02_gbe.md#34-timeouts
[35]: 02_gbe.md#35-non-blocking-channel-operations
[36]: 02_gbe.md#36-closing-channels
[37]: 02_gbe.md#37-range-over-channels
[38]: 02_gbe.md#38-timers
[39]: 02_gbe.md#39-tickers
[40]: 02_gbe.md#40-worker-pools
[41]: 02_gbe.md#41-waitgroups
[42]: 02_gbe.md#42-rate-limiting
[43]: 02_gbe.md#43-atomic-counters
[44]: 02_gbe.md#44-mutexes
[45]: 02_gbe.md#45-stateful-goroutines
[46]: 02_gbe.md#46-sorting
[47]: 02_gbe.md#47-sorting-by-functions
[48]: 02_gbe.md#48-panic
[49]: 02_gbe.md#49-defer
[50]: 02_gbe.md#50-recover
[51]: 03_gbe.md#51-string-functions
[52]: 03_gbe.md#52-string-formatting
[53]: 03_gbe.md#53-text-templates
[54]: 03_gbe.md#54-regular-expressions
[55]: 03_gbe.md#55-json
[56]: 03_gbe.md#56-xml
[57]: 03_gbe.md#57-time
[58]: 03_gbe.md#58-epoch
[59]: 03_gbe.md#59-time-formatting--parsing
[60]: 03_gbe.md#60-random-numbers
[61]: 03_gbe.md#61-number-parsing
[62]: 03_gbe.md#62-url-parsing
[63]: 03_gbe.md#63-sha256-hashes
[64]: 03_gbe.md#64-base64-encoding
[65]: 03_gbe.md#65-reading-files
[66]: 03_gbe.md#66-writing-files
[67]: 03_gbe.md#67-line-filters
[68]: 03_gbe.md#68-file-paths
[69]: 03_gbe.md#69-directories
[70]: 03_gbe.md#70-temporary-files-and-directories
[71]: 03_gbe.md#71-embed-directive
[72]: 03_gbe.md#72-testing-and-benchmarking
[73]: 03_gbe.md#73-command-line-arguments
[74]: 03_gbe.md#74-command-line-flags
[75]: 03_gbe.md#75-command-line-subcommands
[76]: 03_gbe.md#76-environment-variables
[77]: 03_gbe.md#77-logging
[78]: 03_gbe.md#78-http-client
[79]: 03_gbe.md#79-http-server
[80]: 03_gbe.md#80-tcp-server
[81]: 03_gbe.md#81-context
[82]: 03_gbe.md#82-spawning-processes
[83]: 03_gbe.md#83-execing-processes
[84]: 03_gbe.md#84-signals
[85]: 03_gbe.md#85-exit
