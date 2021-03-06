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

# User & Session

##LOGIN
```json
```
Authenticates user based on username / password combination, and creates the user's application session.
### HTTP Request
`GET https://development.api.neuropro.ch/api/login`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username | string | a unique user identifier defined at registration.
password | string | password set by the user.
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
lname | string | Last name.
fname | string | First name.
type| string | [Academic, Commercial].
institution | string | Name of institution.
addr1 | string | First address.
addr2 | string | Second address (optional).
city | string | City name.
country | string | Country name.
email | string | Valid email address. Used for password recovery. No duplicate e-mails allowed.
tel | string | Telephone number.

<aside class="notice">
	All input parameters are optional.
</aside>


### Return
Success/Failure

### Example Call
<code>
[BASE-URL]/api/updateuserprofile;jsessionid=###?username=winam&fname=####&lname=#####&city=####@type=####
</code>
##UPLOADPHOTO
```json
```
Uploads, saves, and retrieves user profile photo
### HTTP Request
`GET https://development.api.neuropro.ch/UPLOADPHOTO`<br>
`POST https://development.api.neuropro.ch/UPLOADPHOTO`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
user_id | int | (Optional) id of user you want to retrieve profile photo of.

<aside class="notice">
<br>To upload photo: HTTP POST To URL
<br>To retrive photo: HTTP GET To URL
</aside>
### Return


### Example Call
<code>
[BASE-URL]profilephoto;JSESSIONID=#####
</code>
<aside class="notice">
Note that this API is a pure HTTP POST / GET, the url does not have '/api' and session id must be passed in tomcat session format: JSESSIONID=####. 
Photos will be upladed/served from S3.
</aside>

#Case Data

##GETATTACHMENTS
```json
```
Returns list of files attached to a case / admission
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETATTACHMENTS`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
admission_id | int | ID of case's admission.
page| int | (optional) Offset of pagination page. If not set all records will be shown.
pagesize| int | (optional) Number of records returned per page (default 10).
### Return

List of attachments

### Example Call
<code>
[BASE-URL]api/getattachments;jsessionid=###?username=winam&admission_id=5001 A case can have one or more addmissions.  Use GETCASEDETAILS (#6) to obtain admission_id
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
case_id| int | ID of case to retrieve.
### Return

Case information separated into two json elements: caseStatic and caseMeta.

### Example Call
<code>
[BASE-URL]api/getcasedetails?session_id=###;jsessionid&username=winam&case_id=nn
</code>
##GETCASELIST
```json
```
Returns a list of Cases that meet on of the following criteria:
1. All cases for all providers to which the user has access
2. Return all cases by provider id
3. Return all cases by provider id and case name (or part of case name)
4. All cases meeting 1 to 3 with in recording date range
5. All cases within a recording date range
6. Return a case by case id

### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASELIST`

### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
page | int | Offset of pagination page. If not set all records will be shown.
pagesize| int | Number of records returned in a single call (default 10). 
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
Return list of case names for all cases of user's provider.
Command can be used to poulate auto-complete textbox in case search screen in client UI.
<aside class="notice">
	Note that the list of available providers can be obtained using GETPROVIDERLIST endpoint
</aside> 
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETCASENAMELIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
page | int | Offset of pagination page. If not set all records will be shown.
pagesize| int | Number of records returned in a single call (default 10). 
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
query_id| int | ID of pre-defined query.
### Return


### Example Call
<code>
[BASE-URL]api/querycases;jsessionid=###?query_id=1
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
query_id| int | ID of pre-defined query.
### Return

ID of deleted item

### Example Call
<code>
[BASE-URL]api/deletecasequery;jsessionid=###?username=winam&query_id=4
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
[BASE-URL]api/getcasequerylist;jsessionid=###
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
queryname| string | Query name.
and | string | and operation in form: field,operant,value (see examples).
or| string | or operation in form: field,operant,value (see examples).
seq_no | int | Query position in the list of user's query.
<aside class="notice">
	Parameters allowed : [Either 'and' or 'or' or both]
</aside>
### Return

Auto generated QUERY_ID

### Example Call
<code>
[BASE-URL]api/savecasequery;jsessionid=###?and=case_id,!=,5000^provider_id,=,1&or=case_gender,=,Female&queryname=Test&seq_no=2
</code>

#Recording

##GETRECORDINGINFO
```json
```
Returns recording information and channel / electrode information for the recording
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETRECORDINGINFO`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id| int | ID of the recording.
### Return

Recording detailed information

### Example Call
<code>
[BASE-URL]api/getrecordinginfo;jsessionid=###?username=winam&recording_id=nnn
</code>

##RECORDINGLIST
```json
```
Returns list of recordings for logged-in user provider.
### HTTP Request
`GET https://development.api.neuropro.ch/api/RECORDINGLIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username| string | Username of logged-in user.
### Return

Recording list with detailed information.

### Example Call
<code>
[BASE-URL]api/recordinglist?username=user
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
recording_id| int | ID of desired recording.
channel| string |  comma separated list of channels. If channel=0 all channels
from_t | string | Relative start of data time in format HH:mm:ss
to_t | string | Relative end of data in format HH:mm:ss
client_sampling| int | [Example: client_sampling=100, every hundredth EEG data point is returned]
weight| int | Weight of data.
filter| string | Available filters: fALPHA , fBETA , fDELTA , fGAMMA , fTHETA , iALPHA , iBETA , iBP , N50 , iBP , iDELTA , iGAMMA , N50 , iGAMMA , iN50 , iTHETA
montage_id| int | reference to montage_def table
filter_id| int | (optional if dfilter is set) reference to filter_def table 
dfilter| string | (optional if filter_id is set) dynamic filter expression of the following format: &lt;filter_type&gt;&#124;&lt;l_frequency&gt;&#124;&lt;u_frequency&gt;&#124;&lt;order&gt;&#124;&lt;design&gt;
filter_type | 2-charecter string | either of : [ LP = Low Pass , HP = High Pass, BP = Band Pass, NF = Notch Filter
l_frequency | int | Lower frequency
u_frequency | int | Upper frequency
order |int| (optional) filter order [default = 2]
design | string | IIR / FIR [default = IIR]
### Return
Recording info + arrays of floats + channel mapping.

### Example Call
<code>

Raw non filtered EEG 50 minutes long for channel 1, 5, and 8:<br>
[BASE-URL]api/geteegdata;jsessionid=###?username=winam&recording_id=100102&channel=1,5,8&from_t=00:00:05&to_t=00:55:00<br>
**<br>
Raw non filtered EEG 2 hours long from all available channels for the recording scales by a factor of 8:<br>
[BASE-URL]api/geteegdata;jsessionid=###?username=winam&recording_id=100102&channel=0&from_t=00:00:00&to_t=02:00:00&weight=8<br>
**<br>
Same as above, with client samply = 256:<br>
[BASE-URL]api/geteegdata;jsessionid=###?username=winam&recording_id=100102&channel=1,5,8&from_t=00:00:05&to_t=00:55:00&wieght=8&client_sampling=256<br>
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
recording_id| int | ID of the rexording 
page| int | (optional) Offset of pagination page. If not set all records will be shown.
pagesize| int | (optional) Number of records returned per page (default 10).
### Return

JSON array of seizures

### Example Call
<code>
[BASE-URL]api/GetSeizures;jsessionid=###?recording_id=100103
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
username | string | Username of logged-in user.
recording_id | int | ID of the recording.
seq| string | a comma separated list of channel numbers in the order of display. If a channel is hidden, do not include in the list.
### Return

success/failure

### Example Call
<code>
/api/savechannelsequence;jsessionid=###?username=winam&recording_id=nnn&seq=n,m,k,l,p
</code>

# MRI

## GETMRITESTINFO

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

## GETMRITESTLIST

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

## GETMRITESTFILES

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


## GETMRITESTFILE

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
`GET https://development.api.neuropro.ch/api/getMriTestFile`

### URL Parameters

Parameter | Type | Description
--------- | ---- | -----------
id|long|ID of image to retrieve.
meta|bool|If this is set to true, DICOM image will be parsed and its metadata will be returned.

<aside class="notice">
	Setting <code>meta=true</code> will cause DICOM image to be pulled from cloud to VmlPro API, slowers the endpoint response significantly.
</aside>

# Annotations/Events

##CREATENOTE
```json
```
Create a user note (event or annotation) on a recording at one point in time or for an interval (from_t, to_t)
### HTTP Request
`GET https://development.api.neuropro.ch/api/CREATENOTE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username| [String] | Username of logged-in user 
type | [char] | Type of note. Can be a,e,s,o : a:annotation, e:event, s:seizure, o:other
note |[String] | Content of the note (comment)
from_t |string | Relative start time of event (note) in format h*:mm:ss
to_t |string | Relative end time of event (note) in format h*:mm:ss
c_label| [String] | Channel label
recording_id| [int] | Recording id to add note to
channel |[int] | number of channel of note. If this is set to 0, note will be added on all channels.
seizure_id |[int] | (optional)  Reference to seizure. Default is 0.
duration |[int] | (optional) Duration of the event in seconds. Default is 0.
group |[int] | (optional) ID of group. default is 1
category| [int] | (optional) ID of category. default is 1
from_ts |[long] | (optional) Relative start time of event in ms. If set, from_t is not used anymore. For example 82123 which is 000:01:22.123
to_ts |[long] | (optional) Relative end time of event in ms. If set, from_t is not used anymore

<aside class="notice">
	Type should be a for annotation, e for event, s for seizure and o for other
</aside>

### Return

Json array of note IDs created

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
channel | [int] | (optional) Number of channel to get notes. Default is 0, will get notes from all channels.
recording_id | [int] | Id of desired recording.
username | [String] | Username of logged-in user.
type | [Char] | Type of notes to get. Can be a,e,s,o : a:annotation, e:event, s:seizure, o:other
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
note_id | [int] | id of note to delete.
note | [String] | Content of the note (comment)
from_t | string | Relative start time of event (note) in format h*:mm:ss
to_t | string | Relative end time of event (note) in format h*:mm:ss
username | [String] | Username of logged-in user.
### Return
### Example Call
<code>
</code>

##DELETENOTE
```json
```
Deletes a user-created note.
<aside class="notice">
	Note that this endpoint can only delete notes created by LOGGED IN User.
</aside>
### HTTP Request
`GET https://development.api.neuropro.ch/api/DELETENOTE`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
note_id | [int] | id of note to delete.
username | [String] | Username of logged-in user.

### Return

### Example Call
<code>
</code>

##SAVENOTEGROUP
Creates new notes group.
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SaveNoteGroup`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
group | [String] | Name of new group.
username | [String] | Username of logged-in user.
### Return


### Example Call
<code>
</code>
##SAVENOTECATEGORY
Creates new category for notes based on group.
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SaveNoteCategory`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
group_id | [int] | Id of group to add category in.
category | [String] | Name of new category.
username | [String] | Username of logged-in user.
### Return


### Example Call
<code>
</code>
##DELETENOTECATEGORY
Deletes notes group.
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/DeleteNoteCategory`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
group_id | [int] | ID of group to delete.
username | [String] | Username of logged-in user.
### Return


### Example Call
<code>
</code>

#Bookmark

##GETBOOKMARKLIST
```json
{
  "BOOKMARKLIST": [
    {
      "id": 59,
      "name": "J list",
      "seq_no": 0
    },
    {
      "id": 61,
      "name": "Epi set 1",
      "seq_no": 1
    },
    ],
  "SUCCESS": 1
}
```
Returns list of user's bookmarks.
### HTTP Request
`GET https://development.api.neuropro.ch/api/GETBOOKMARKLIST`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
NONE||
### Return

ID of generated bookmark item

### Example Call
<code>
[BASE-URL]api/addtobookmark?session_id=###;jsessionid#@username=winam&case_id=nnn&seq_no=m
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
case_id| int | Id of the case.
seq_no| int | Position of the bookmark in user's bookmark list.
list_id | ID of the bookmark list.
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
[BASE-URL]api/removebookmark;jsessionid=###?username=winam&bookmark_id=nnn
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
[BASEURL]api/updatebookmark;jsessionid=###?username=winam&bookmark_id=nn&seq_no=mm
</code>

#Signal Processing
##POWERSPECTRUM
```json
```
Generate a power spectrum of the recording or a segment of the recording for 1 or more channels
### HTTP Request
`GET https://development.api.neuropro.ch/api/POWERSPECTRUM`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
recording_id | long | Id of recording desired. 
channel| string | [integer or comma separated list; 0 for all channels]
from_t | string | Relative start time of analysis in format hh:mm:ss
to_t | string | Relative end time of analysis in format hh:mm:ss
token | |
Python | | 
based | | 
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
color_map| string | Name of the color map. (default "")
rawdata | boolean | (true/false) returns json or images.
recording_id | int | ID of the recording.
channel | string | Channel number of comma-separated list of channels
from_t | string | Relative start time of analysis in format hh:mm:ss
to_t | string | Relative end time of analysis in format hh:mm:ss
token | |
### Return

Json array containing links to images or json data

### Example Call
<code>
</code>


#Montage
##SAVEMONTAGEDEF
```json
```
Saves a new montage configuration to DB
### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVEMONTAGEDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username | string | Username of logged-in user. 
montage_label | string | Label for new montage.
montage_type | string | either of : [ "Longitudinal bipolar" , "Transverse bipolar" , "Referential" , "Referential average" ,"Custom" ]
montage_mapping| string | [e.g.: FP1:A1;FP2:A1;A1:A1;A2:A1, ...]
<aside class="notice">
	Pre-built montages are available to all users, and users can define new ones.
</aside>
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
montage_id | int | ID of existing custom montage.
montage_mapping | string | [e.g.: FP1:A1;FP2:A1;A1:A1;A2:A1, ...].
montage_label | string | (optional) New label for montage.
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
montage_id | int  | ID of existing custom montage.
### Return


### Example Call
<code>
</code>

#Filters
##SAVEFILTERDEF
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVEFILTERDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username | string | Username of logged-in user. 
recording_id| int | ID of the recording.
filter_label | string | Label for new filter.
filter_design | string | Filter definition.
filter_type| string | either of : ('LP','HP','BP','NF')
l_frequency | float | Low frequency.
h_frequency | float` | High frequency.
order | int | Order of the filter for recording.
comment | string | Comment about filter.
### Return


### Example Call
<code>
</code>
##EDITFILTERDEF
```json
```

### HTTP Request
`GET https://development.api.neuropro.ch/api/EDITFILTERDEF`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
username | string | Username of logged-in user. 
filter_id| int | ID of filter to edit.
filter_label | string | Label for new filter.
filter_design | string | Filter definition.
filter_type| string | either of : ('LP','HP','BP','NF')
l_frequency | float | Low frequency.
h_frequency | float` | High frequency.
order | int | Order of the filter for recording.
comment | string | Comment about filter.
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
filter_id| int | ID of filter to delete.
### Return


### Example Call
<code>
</code>

#Other
##SAVECOMMENT
```json
```
Updates comment field on a case, admission, electrode, recording, events, run, or seizure.
### HTTP Request
`GET https://development.api.neuropro.ch/api/SAVECOMMENT`
### Query Parameters
Parameter | Type | Description
--------- | ---- | -----------
object_id | int | ID of the record.
type | string | Can have the following values: case, admission, electrode, recording, events, run, seizure.
comment| string | New comment text.
### Example Call
<code>
Update comment on case id=nn<br>
[BASE-URL]api/savecomment?session_id=####&username=winam&object_id=nn&type=case&comment=My+case+comment<br>
**<br>
Update comment on run id=mm<br>
[BASE-URL]api/savecomment?session_id=####&username=winam&object_id=nn&type=case&comment=My+run+comment<br>
<br>
Update comment on events id=nn<br>
[BASE-URL]api/savecomment?session_id=####&username=winam&object_id=mm&type=events&comment=My+case+comment
</code>

