# Insert data into Google Sheets

Next, let's transform data returned by the ISS API and save it to Google Sheets. This example builds on the workflow created in [previous sections](/quickstart/hello-world/) and will cover how to:

[[toc]]

### Set up a Google Sheet for this example

First, create a Google Sheet with the columns `Latitude`, `Longitude`, `Timestamp`, and `Date/Time` in the first row:

![image-20210517195215566](./image-20210517195215566.png)  

### Transform a timestamp using the `luxon` npm package

Next, select the most recent event and inspect the exports for `steps.get_iss_position`. We can see that the `timestamp` field returned by the API is not a friendly, human-readable date/time:

![image-20210525181359535](./image-20210525181359535.png)

Let's fix that by using the `luxon` npm package to transform the timestamp into a value that is human-readable — and one that Google Sheets will interpret as a date/time. Based on a quick Google Search, the date/time format expected by Google Sheets is `yyyy-MM-dd HH:mm:ss`. 

Click the **+** button to add a new step after `steps.get_iss_position` and select **Run Node.js code**. 

![image-20210525181449209](./image-20210525181449209.png)

Then add the following code to convert the timestamp to a Google Sheets compatible date/time and export it from the code step:

```javascript
const { DateTime } = require('luxon')

return DateTime.fromSeconds(steps.get_iss_position.$return_value.timestamp).toFormat('yyyy-MM-dd HH:mm:ss');
```

Let's also change the name of the code step to `steps.format_datetime`

![image-20210525181543435](./image-20210525181543435.png)

**Deploy** and test your changes (load the endpoint URL or use the **Replay Event** or **Send Test Event** buttons).

Next, select the most recent event. You should see a human-readable date/time as the return value for `steps.format_datetime` (in GMT).

![image-20210525181704209](./image-20210525181704209.png)

### Connect an account to authenticate API requests

Then click the **+** button to add a new step after `steps.format_datetime` and select the **Google Sheets** app:

![image-20210525181736082](./image-20210525181736082.png)

Then select the **Add Single Row** action:

![image-20210525181801075](./image-20210525181801075.png)

To configure the step, first connect your Google Sheets account. When you click on **Connect Google Sheets** Pipedream will open a popup window where you can sign in to connect your account.

![image-20210525181917662](./image-20210525181917662.png)

When prompted by Google, allow Pipedream access:

<img src="./image-20210517181653424.png" alt="image-20210517181653424" style="zoom:25%;" />

After you connect your account, Pipdream will securely store an authorization token that you can use in actions and code steps to authenticate API requests for Google Sheets (we'll cover how to use this token in code steps later in this guide). If you connect an OAuth account (like Google Sheets), then Pipedream will automatically refresh the token without further action from you.

Next, select your **Drive**, **Spreadsheet** and **Sheet Name** from the drop down menus.

![image-20210525182234399](./image-20210525182234399.png)

### Pass exports from a previous step to an action

Next, let's configure the cells / column values. First, we'll use the object explorer to select a value. The object explorer is automatically loaded whenever you focus in an action input. You can expand any item and then select the reference you want to insert.

![image-20210525182314235](./image-20210525182314235.png)

Another option is to explore the exports for a step and click on the **Copy Path** link. Then paste the reference into the action input.

![image-20210525182402438](./image-20210525182402438.png)

The final option is to use autocomplete — add double braces `{{ }}` and start typing between them to get autocomplete the same way you do in code steps. 

![autocomplete](./autocomplete.gif)

Since we want to add four columns of data with the latitude, longitude, timestamp and the formatted date time (in that order), add the following references to the **Cells / Column Values** inputs:

**[0]:** <code v-pre>{{steps.get_iss_position.$return_value.iss_position.latitude}}</code>

**[1]:** <code v-pre>{{steps.get_iss_position.$return_value.iss_position.longitude}}</code>

**[2]:** <code v-pre>{{steps.get_iss_position.$return_value.timestamp}}</code>

**[3]:** <code v-pre>{{steps.format_datetime.$return_value}}</code>

Your fully configured step should look similar to this:

![image-20210525183257888](./image-20210525183257888.png)

Next, **Deploy** your changes and reload the endpoint URL in your browser. 

![reload-iss-sheets](./reload-iss-sheets.gif)

When you check the workflow, you should see exports from the add row to sheets step:

![image-20210525183834078](./image-20210525183834078.png)



Finally, when you check Google Sheet you should see data about the ISS position.

![image-20210525184058476](./image-20210525184058476.png)



**If you loaded the URL in your web browser, you'll actually see two events. We'll fix that in the next example. [Take me to the next example &rarr;](../end-workflow-early/)**


