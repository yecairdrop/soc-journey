# Sysmon Event IDs
| ID | Event | Key fields |
| --- | --- | --- |
| 1 | Process create | Image, CommandLine, ParentImage, User, Hashes |
| 3 | Network connection | Image, DestinationIp, DestinationPort |
| 5 | Process terminated | Image |
| 7 | Image loaded | ImageLoaded, Signed |
| 8 | CreateRemoteThread | SourceImage, TargetImage |
| 10 | Process access | SourceImage, TargetImage, GrantedAccess |
| 11 | File create | Image, TargetFilename |
| 12/13/14 | Registry key / value / rename | TargetObject |
| 15 | File stream created | TargetFilename |
| 17/18 | Pipe created / connected | PipeName |
| 22 | DNS query | QueryName, Image |
| 23/26 | File delete | TargetFilename |
| 25 | Process tampering | Image |
