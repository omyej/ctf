# Out of Site #

**Category:**	Web Security  
**Points:**	5

**Prompt:** 
* The flag submission system lets us see the strings you submitted even when they're wrong. Since some of you may consider that a privacy issue, we decided to demo a flag validation system that checks it client-side before sending the flag to us for checking. Why don't you give it a try: http://challenge.acictf.com:47728 

**Hints:** 
* How does your browser know what the right key is to do the check? 
* If only there were a way to control+U're view of what the server sent you. 

**Solution:**
* Click link and go to developer tools in firefox (F12)
* Right click on text field and click inspect element 
* Find the "pattern" field 
* ACI{hidden_in_plain_site_dd62edef} 
