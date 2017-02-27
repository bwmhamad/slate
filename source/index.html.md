---
title: Neuropro API Documentation

language_tabs:
  - Response

toc_footers:
  - <a href='#'>Neuropro WebApp</a>
  - <a href='#'>Dev VmlPro API Instance</a>

includes:
  

search: true
---

# Introduction

NeuroPro incorporates specialists from the fields of computer science, neurophysiology, bioengineering, and product and user-interface design. 
This combination of skills allows us to adopt an informed inter-disciplinary approach to the specific challenges facing those working in brain science. 
<br>
VmlPro Api is the data source of all other clients in Neuropro, providing variety of tools and analistic options for the developers on rival. 
<img src="images/api.png">

# Authentication

VmlPro Api uses endpoint to retrieve SESSIONID that can be later used for endpoint access.

```json
#Successful response:
{
  "SUCCESS": 1,
  "SESSION_ID": "0A1A9BF836664AC4E9A9BB640E82B34C",
  "user": {
    "user_country": "Lebanon",
    "user_access_level": null,
    "provider_id": 8,
    "user_type": "",
    "user_city": "Beirut",
    "user_telephone": "70895649",
    "user_firstName": "Mohammed",
    "user_email": "mohammed.saad@lurner.com",
    "user_address1": "Beirut - Lebanon",
    "user_address2": "",
    "user_name": "msaad",
    "user_lastName": "Saad",
    "user_institution": "Neuropro",
    "user_id": 37
  }
}
```
```json
#Login failed:
{
  "SUCCESS": 0,
  "SESSION_ID": "",
  "ERROR": "Login failed"
}
```
SESSION_ID can be sent either by cookie or by assigning <code>;jsessionid={{session_id}}</code> after the endpoint url.

<aside class="notice">
	You must replace <code>{{session_id}}</code> with key from login endpoint.
</aside>

# Endpoints

## Get Test



```json
{
  "SUCCESS": 1,
  "TEST": {
    "id": 1102,
    "provider_id": 1,
    "mri_date": "20/9/2002",
    "hippocampal_atrophy": "-",
    "hippocampal_intensity_incr": "-",
    "hippocampal_malrotation": "-",
    "hippocampal_tumor": "-",
    "amygdala_atrophy": "-",
    "amygdala_intensity_incr": "-",
    "amygdala_tumor": "-",
    "cavernoma": "-",
    "angioma": "-",
    "aneurysma": "-",
    "venous_dysplasia": "-",
    "dural_fistula": "-",
    "tumor": "-",
    "cortical_dysplasia": "l",
    "tubers": "-",
    "polymicrogyria": "-",
    "pachygyria": "-",
    "lessencephaly": "-",
    "schizencephaly": "r",
    "hemimegalencephaly": "-",
    "contusion": "-",
    "ischemic_lesion": "-",
    "intracerebral_bleeding": "-",
    "atrophy": "-",
    "arachnoid_cyst": "-",
    "temp_lobe_intensity_incr": 0,
    "gray_white_matter_blurring": 0,
    "contrast_enhancement": 0,
    "micrencephaly": 0,
    "megalencephaly": 0,
    "hypothalamic_hamartoma": 0,
    "hydrocephalus": 0,
    "other_cort_developm_malform": "aa",
    "files": [
      {
        "id": 1002102,
        "mri_id": 1102,
        "diagnosis": 1102,
        "image": 1002102,
        "header": 1001102,
        "slice": 0,
        "provider_id": 1,
        "series": "Head/Neck series"
      }
    ]
  }
}
```

Retrieves information about specific MRI test.

### HTTP Request

`GET https://development.api.neuropro.ch/api/getmritestinfo`

### Query Parameters

Parameter | Type | Description
--------- | ---- | -----------
id | long | ID of test to retrieve

## Get Test List

```json
{
  "SUCCESS": 1,
  "TESTS": [
	{
		...
	},
	{
		...
	}
  ]
}
```

Retrieves a list of MRI tests based on authenticated user's provider.

### HTTP Request

`GET https://development.api.neuropro.ch/api/getmritestlist`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
files|bool|If this set to true, list of files will be included with each MRI test. Default: true

## Get Image List

```json
{
  "SUCCESS": 1,
  "FILES": [
    {
      "id": 1002102,
      "mri_id": 1102,
      "diagnosis": 1102,
      "image": 1002102,
      "header": 1001102,
      "slice": 0,
      "provider_id": 1,
      "series": "Head/Neck series"
    },
    {
      "id": 73002102,
      "mri_id": 1102,
      "diagnosis": 73102,
      "image": 73002102,
      "header": 73001102,
      "slice": 0,
      "provider_id": 1
    }
  ]
}
```

Retrieves list of DICOM images assosiated with MRI test.

### HTTP Request
`GET /getMriTestFiles`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
mri_id|long|ID of desired MRI test.


## Get Image List

```json
{
  "SUCCESS": 1,
  "TEST": {
    "id": 1002102,
    "mri_id": 1102,
    "diagnosis": 1102,
    "image": 1002102,
    "header": 1001102,
    "slice": 0,
    "provider_id": 1,
    "series": "Head/Neck series",
    "url": "https://vmlpro-mri.s3.amazonaws.com/..."
  }
}
```

Retrieves DICOM image by its id along with cloud download link and meta-data.  

### HTTP Request
`GET /getMriTestFile`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
id|long|ID of image to retrieve.
meta|bool|If this is set to true, DICOM image will be parsed and its metadata will be returned.

<aside class="notice">
	Setting <code>meta=true</code> will cause DICOM image to be pulled from cloud to VmlPro API, slowers the endpoint response significantly.
</aside>



##>Login
```json
```
Authenticates user based on username / password combination, and creates the user's application session.
### HTTP Request
`GET https://development.api.neuropro.ch/api/>Login`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
Username:| |  username [string] a unique user identifier defined at registration
Password:| |  password [string] the password set by the user
### Return

Session_ID: [string] unique identifier created by the server and is associated with the user session. The session id parameters should be passed to subsequent API calls.  If authentication fails, null is returned.

### Example Call
<code>
[BASE-URL]/api/login?username=<user>&password=<*****>
</code>
##LOGOFF
```json
```
Closes user session and logs user off. Not implemented
### HTTP Request
`GET https://development.api.neuropro.ch/api/LOGOFF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
None | | 
### Return

None

### Example Call
<code>
[BASE-URL]/api/logoff
</code>
##UPDATEUSERPROFILE
```json
```
Updates user profile information
### HTTP Request
`GET https://development.api.neuropro.ch/api/UPDATEUSERPROFILE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
lname | | 
fname | | 
type| | [Academic, Commercial]
institution | | 
addr1 | | 
addr2 | | 
city | | 
country | | 
email | | 
tel
All| | input parameters are optional.
### Return


### Example Call
<code>
[BASE-URL]/api/updateuserprofile?session_id=###;jsessionid#&username=winam&fname=####&lname=#####&city=####@type=####
</code>
##UPLOADPHOTO
```json
```
Uploads, saves, and retrieves user profile photo
### HTTP Request
`GET https://development.api.neuropro.ch/api/UPLOADPHOTO`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
To| | Upload photo: HTTP POST To URL
To| | retrive photo: HTTP GET To URL
### Return


### Example Call
<code>
[BASE-URL]profilephoto;JSESSIONID=#####
Note that this API is a pure HTTP POST / GET, the url does not have '/api' and session id must be passed in tomcat session format: JSESSIONID=####
Photos will be upladed/served from S3
</code>
##GETATTACHMENTS
```json
```
Returns list of files attached to a case / admission
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETATTACHMENTS`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
admission_id(int) | | 
page| | optional
pagesize| | (default 10)"            optional
### Return

List of attachments

### Example Call
<code>
[BASE-URL]api/getattachments?session_id=###;jsessionid#&username=winam&admission_id=5001 A case can have one or more addmissions.  Use GETCASEDETAILS (#6) to obtain admission_id
</code>
##GETCASEDETAILS
```json
```
Returns case information with available static and dynamic meta data and available admission records. Note that a case can have one or more admission records.  An EEG recording is associated with an admission record.
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASEDETAILS`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
case_id| | (int)
### Return

Case information separated into two json elements: caseStatic and caseMeta

### Example Call
<code>
[BASE-URL]api/getcasedetails?session_id=###;jsessionid&username=winam&case_id=nn
</code>
##GETCASELIST
```json
```
Returns a list of Cases that meet on of the following criteria
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASELIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
All| | cases for all providers to which the user has access
Return| | all cases by provider id
Return| | all cases by provider id and case name (or part of case name)
All| | cases meeting 1 to 3 with in recording date range
All| | cases within a recording date range
Return| | a case by case id
page | | 
pagesize| | (default 10)
### Return

Data returned to client:
- All case information
- # of cases meeting criteria
- All recording information for each case
- All block information for each recording

### Example Call
<code>
**
[BASE-URL]api/getcaselist?and=case_id,=,5001
returns case info for case_id=5001
**
[BASE-URL]api/getcaselist?and=case_id,<,5001^case_gender,=,Female
returns all cases whose case_id < 5001 AND gender = 'Female'
**
[BASE-URL]api/getcaselist?or=provider_id,=,1^provider_id,=,2
returns all cases provided by provider_id=1 or provider_id=2
**"
</code>
##GETCASENAMELIST
```json
```
Return list of case names for all cases of a provider
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASENAMELIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
provider_id| | None…provider will be retrieved from logged in user profile
Command can be used to poulate auto
complete| | textbox in case search screen in client UI.
Note that the list of available providers can be obtained using GETPROVIDERLIST command (# 9)"
provider_id| | (int)
page | | 
pagesize| | (default 10)
### Return

List of cases:
- case name
- case id

### Example Call
<code>
[BASE-URL]api/getcasenamelist?session_id=###;jsessionid&username=winam&provider_id=1
</code>
##QUERYCASES
```json
```
Returns a list of cases by executing a saved, dynamically-created case query
### HTTP Request
`GET https://development.api.neuropro.ch/api/QUERYCASES`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
query_id| | (int)
### Return


### Example Call
<code>
[BASE-URL]api/querycases?session_id=###;jsessionid#&query_id=1
</code>
##DELETECASEQUERY
```json
```
Deletes case query from user list of saved queries
### HTTP Request
`GET https://development.api.neuropro.ch/api/DELETECASEQUERY`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
query_id| | (int)
### Return

ID of deleted item

### Example Call
<code>
[BASE-URL]api/deletecasequery?session_id=###;jsessionid#&username=winam&query_id=4
</code>
##GETCASEQUERYLIST
```json
```
Returns list of user saved, dynamically-created case queries
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASEQUERYLIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
None | | 
### Return

List of saved queries

### Example Call
<code>
[BASE-URL]api/getcasequerylist?session_id=###;jsessionid#
</code>
##SAVECASEQUERY
```json
```
Saves case query statement with a name identifier for a user
### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVECASEQUERY`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
queryname| | (string)
and | | 
or| | [Either 'and' or 'or' or both]
seq_no | | 
### Return

Auto generated QUERY_ID

### Example Call
<code>
[BASE-URL]api/savecasequery?session_id=###;jsessionid#&and=case_id,!=,5000^provider_id,=,1&or=case_gender,=,Female&queryname=Test&seq_no=2
</code>
##GETEEGDATA
```json
```
Main API for accessing raw EEG data.
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETEEGDATA`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id| | (int)
channel| | (String) comma separated list of channels. If channel=0 all channels
from_t| | (string: hh:mm:ss)
to_t| | (string: hh:mm:ss)
Available filters:
fALPHA | | 
fBETA | | 
fDELTA | | 
fGAMMA | | 
fTHETA | | 
iALPHA | | 
iBETA | | 
iBP | | 
N50 | | 
iBP | | 
iDELTA | | 
iGAMMA | | 
N50 | | 
iGAMMA | | 
iN50 | | 
iTHETA | | 
client_sampling| | (int)
[Example: client_sampling=100, every hundredth EEG data point is returned]
weight| | (int)
filter:| | string as defined in E17
montage_id| | [reference to montage_def table]
filter_id| | [reference to filter_def table]
or
dfilter| | [dynamic filter expression of the following format:
<filter_type>|<l_frequency>|<u_frequency>|<order>|<design>
filter_type =
charecter| | string:
LP = Low Pass
HP = High Pass
BP = Band Pass
NF = Notch Filter
l_frequency = Lower frequency
u_frequency = Upper frequency
order = filter order [default = 2]
design = IIR / FIR [default = IIR]
Recording info + arrays of floats + channel mapping
Raw non filtered EEG 50 minutes long for channel 1, 5, and 8:
### Return


### Example Call
<code>
[BASE-URL]api/geteegdata?session_id=###;jsessionid&username=winam&recording_id=100102&channel=1,5,8&from_t=00:00:05&to_t=00:55:00
**
Raw non filtered EEG 2 hours long from all available channels for the recording scales by a factor of 8:
[BASE-URL]api/geteegdata?session_id=###;jsessionid&username=winam&recording_id=100102&channel=0&from_t=00:00:00&to_t=02:00:00&weight=8
**
Same as above, with client samply = 256
[BASE-URL]api/geteegdata?session_id=###;jsessionid&username=winam&recording_id=100102&channel=1,5,8&from_t=00:00:05&to_t=00:55:00&wieght=8&client_sampling=256
</code>
##GETRECORDINGINFO
```json
```
Returns recording information and channel / electrode information for the recording
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETRECORDINGINFO`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id| | (int)
### Return

Recording detailed information

### Example Call
<code>
[BASE-URL]api/getrecordinginfo?session_id=###;jsessionid&username=winam&recording_id=nnn
</code>
##GETSEIZURES
```json
```
Returns seizure information for a recording (if available)
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETSEIZURES`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id| | (int) page
pagesize (default 10)
### Return

JSON array of seizures

### Example Call
<code>
[BASE-URL]api/GetSeizures?session_id=###;jsessionid&recording_id=100103
</code>
##CREATENOTE
```json
```
Create a user note (event or annotation) on a recording at one point in time or for an interval (from_t, to_t)
### HTTP Request
`GET https://development.api.neuropro.ch/api/CREATENOTE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
note| | (string)
type| | [Annotation | Event | Other]
sub_type| | [reference to annotation or event table depending on type]
recording_id | | 
channel| | (if channel = 0 then note will be created for all channels)
from_t | | 
to_t | | 
seizure_id | | 
duration| | (default 0)
group| | (default 1)
category| | (default 1)
Type should be a for annotation, e for event, s for seizure and o for other
### Return

Json array of note IDs created

### Example Call
<code>
</code>
##DELETENOTE
```json
```
Deletes a user-created note
### HTTP Request
`GET https://development.api.neuropro.ch/api/DELETENOTE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
note_id | | 
### Return


### Example Call
<code>
</code>
##GETNOTES
```json
```
Retrieve list of user-created notes
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETNOTES`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id | | 
type | | 
channel| |          (default 0 all channels)
### Return


### Example Call
<code>
</code>
##UPDATENOTE
```json
```
Updates a user-created note
### HTTP Request
`GET https://development.api.neuropro.ch/api/UPDATENOTE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
note_id | | 
note| | (string)
from_t | | 
to_t | | 
### Return


### Example Call
<code>
</code>
##ADDTOBOOKMARK
```json
```
Adds a case to user's bookmark list for future quick access
### HTTP Request
`GET https://development.api.neuropro.ch/api/ADDTOBOOKMARK`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
case_id| | (int)
seq_no| | (int)
3 – list_id
### Return

ID of generated bookmark item

### Example Call
<code>
[BASE-URL]api/addtobookmark?session_id=###;jsessionid#@username=winam&case_id=nnn&seq_no=m
</code>
##REMOVEBOOKMARK
```json
```
Removes bookmark item from user's bookmarks
### HTTP Request
`GET https://development.api.neuropro.ch/api/REMOVEBOOKMARK`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
bookmark_id| | (int)
### Return

ID of deleted item

### Example Call
<code>
[BASE-URL]api/removebookmark?session_id=###;jsessionid#&username=winam&bookmark_id=nnn
</code>
##UPDATEBOOKMARK
```json
```
Update bookmark item in user's bookmarks
### HTTP Request
`GET https://development.api.neuropro.ch/api/UPDATEBOOKMARK`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
bookmark_id| | (int)
seq_no| | (int)
### Return

ID of updated item

### Example Call
<code>
[BASEURL]api/updatebookmark?session_id=###;jsessionid#&username=winam&bookmark_id=nn&seq_no=mm
</code>
##SAVECHANNELSEQUENCE
```json
```
Saves channel display sequence for a recording per user.
### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVECHANNELSEQUENCE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username | | 
recording_id | | 
seq| | [a comman
separated| | list of channel numbers in the order of display. If a channel is hidden, do not include in the list]
### Return

success/failure

### Example Call
<code>
/api/savechannelsequence?session_id=<####>&username=winam&recording_id=nnn&seq=n,m,k,l,p
</code>
##POWERSPECTRUM FFT
```json
```
Generate a power spectrum of the recording or a segment of the recording for 1 or more channels
### HTTP Request
`GET https://development.api.neuropro.ch/api/POWERSPECTRUM FFT`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id | | 
channel| | [integer or comma
separated| | list; 0 for all channels]
from_t | | 
to_t"
5 | | 
token
Python | | 
based | | 
### Return


### Example Call
<code>
</code>
##SAVEMONTAGEDEF
```json
```
Saves a new montage configuration to DB
### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVEMONTAGEDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
user | | 
montage_label | | 
montage_type | | 
montage_mapping| | [e.g.: FP1:A1;FP2:A1;A1:A1;A2:A1, ...]
Comment"
Pre | | 
built| | montages are available to all users, and users can define new ones.
Montag_type
"Longitudinal bipolar"
"Transverse bipolar"
"Referential"
"Referential average"
"Custom"
### Return


### Example Call
<code>
</code>
##EDITMONTAGEDEF
```json
```
Update an existing montage confugration in DB
### HTTP Request
`GET https://development.api.neuropro.ch/api/EDITMONTAGEDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
montage_id | | 
montage_mapping"
3| | – montage_label (optional)
### Return


### Example Call
<code>
</code>
##DELETEMONTAGEDEF
```json
```
Removes an existing montage configuration in DB
### HTTP Request
`GET https://development.api.neuropro.ch/api/DELETEMONTAGEDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
montage_id | | 
### Return


### Example Call
<code>
</code>
##SAVEFILTEREDEFSAVEFILTERDEF
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVEFILTEREDEFSAVEFILTERDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
user | | 
filter_label | | 
filter_design | | 
filter_type| | ('LP','HP','BP','NF'   )
l_frequency | | 
uh_frequency | | 
order | | 
comment | | 
### Return


### Example Call
<code>
</code>
##EDITFILTEREDEFEDITFILTERDEF
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/EDITFILTEREDEFEDITFILTERDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
filter_id | | 
filter_label | | 
filter_design | | 
filter_type | | 
l_frequency | | 
uh_frequency | | 
order | | 
comment | | 
### Return


### Example Call
<code>
</code>
##DELETEFILTERDEF
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/DELETEFILTERDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
filter_id | | 
### Return


### Example Call
<code>
</code>
##SaveNoteGroup
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SaveNoteGroup`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
1 | | 
group | | 
### Return


### Example Call
<code>
</code>
##SaveNoteCategory
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SaveNoteCategory`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
1 | | 
group_id
2 | | 
category" | | 
### Return


### Example Call
<code>
</code>
##DeleteNoteCategoryCommand
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/DeleteNoteCategoryCommand`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
1 | | 
category_id | | 
### Return


### Example Call
<code>
</code>
##HEATMAP
```json
```
Calculates a heat map representation of an EEG recording
### HTTP Request
`GET https://development.api.neuropro.ch/api/HEATMAP`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
1 | | 
color_map| | (default "")
2 – rawdata (true/false) returns json or images
3
recording_id
4| | – channel
5 – from_t
6 – to_t
7 – token
### Return

Json array containing links to images or json data

### Example Call
<code>
</code>


