+++
date = "2017-02-16T22:12:47+09:00"
title = "App Manager Scanning"
tags = ["app", "dev"]
categories = ["java", "Python"]
description = ""
+++

Close the application and restart it again (there is one request that we want to see).

First request is a weird one. It is asking the server for the current date and time via a GET request to http://appmanager.filehippo.com/api/v1/DateTime.

Get DateTime
 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
GET /api/v1/DateTime HTTP/1.1
User-Agent: download_manager
ClientId: 4dfe2b82-501c-4324-83ce-6d49a96cdf61
AppManagerVersion: 2.0.0.392
AccessToken: 2N0+YwVnSXph9L0ZuS8zOmnSvvKHq10QYMtuM0GdUVmLp067RfBBpw==
RequestTime: 2016-05-17T04:47:25.3968037Z
Host: appmanager.filehippo.com
Connection: close

Response body:
{"DateTime":"2016-05-17T04:47:57.0719162Z","Status":0,"Message":null}
Note the AccessToken which is a 40 byte blob in base64. For this request we can remove the AccessToken and it works. Why would the app get the date and time from the server?

Then the app requests http://appmanager.filehippo.com/api/v1/ProgramDefinitions which is a list of all applications that are supported by the App Manager. This request also contains the AccessToken header which contains a base64 encoded 40 byte blob. It seems like this access token is also time sensitive because if you send the request to Repeater and then send it after 10 minutes the response is 401 Unauthorized while this did not happen in the DateTime request. At this point we do not know where this access token comes from because it is not in any of the responses (up until now we have only done the proxy test). There is also a 32 byte GUID named ClientId. Based on the previous request and the ClientId header, you can probably guess how the AccessToken is generated.

Retrieving program definitions
 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
Request:
GET /api/v1/ProgramDefinitions HTTP/1.1
User-Agent: download_manager
ClientId: ...
AppManagerVersion: 2.0.0.392
AccessToken: ...
RequestTime: 2016-05-15T21:56:10.2127122Z
Host: appmanager.filehippo.com
Connection: close

Response body:
{
    "Status": 0,
    "Message": null,
    "Definitions": [
        {
            "DetectionXML": "<fp>\r\n    <fn>abiword.exe</fn>\r\n    <pfloc>abisuite2\\abiword\\bin</pfloc>\r\n</fp>\r\n<fp>\r\n    <fn>abiword.exe</fn>\r\n    <pfloc>abiword\\bin</pfloc>\r\n</fp>\r\n<reg>\r\n    <k hive=\"hklm\" key=\"software\\abisuite\\abiword\\v2\">\r\n        <v name=\"Version\"/>\r\n    </k>\r\n</reg>\r\n<reg>\r\n    <k hive=\"hklm\" key=\"software\\abiword\\v2\">\r\n        <v name=\"Version\"/>\r\n    </k>\r\n</reg>",
            "ProgramId": 135
        },
        {
            "DetectionXML": "<fp>\r\n    <fn>ACDSee*.exe</fn>\r\n    <pfloc>ACD Systems\\ACDSee\\*</pfloc>\r\n</fp>\r\n<fp>\r\n    <fn>ACDSee*.exe</fn>\r\n    <pfloc>ACD Systems\\ACDSee\\17.0</pfloc>\r\n</fp>\r\n<fp>\r\n    <fn>ACDSee*.exe</fn>\r\n    <pfloc>ACD Systems\\ACDSee\\18.0</pfloc>\r\n</fp>",
            "ProgramId": 83
        },
        ...
Next is a POST request to http://appmanager.filehippo.com/api/v1/ScanResults with a JSON payload in the body. The payload contains information about installed programs. The access token is also different.

POST ScanResults
 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
parts of request's body:
{
    "Programs": [
        {
            "Registry": [],
            "Id": 298,
            "Files": [
                {
                    "VerPv": "2.0.0.392",
                    "VerPvr": "2.0.0.392",
                    "Len": 10566352,
                    "VerFv": "2.0.0.392",
                    "VerFvr": "2.0.0.392",
                    "File": "C:\\Program Files (x86)\\FileHippo.com\\FileHippo.AppManager.exe",
                    "Md5": "6798339CF7C87F5F567A8F050614D6B8"
                }
            ]
        },
        {
            "Registry": [],
            "Id": 12,
            "Files": [
                {
                    "VerPv": "46.0.1",
                    "VerPvr": "46.0.1.0",
                    "Len": 392136,
                    "VerFv": "46.0.1",
                    "VerFvr": "46.0.1.5966",
                    "File": "C:\\Program Files (x86)\\Mozilla Firefox\\firefox.exe",
                    "Md5": "7DF8845A1CF92C227E81DBBC6F6434DF"
                }
            ]
        }
        ...
}
The response contains links to the applications that have updates. For example in this case, Firefox 47 Beta 2.0 is available.
