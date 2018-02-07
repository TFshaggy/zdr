## About
This script is used to manage rotations within Zendesk.

-  Search filter changes to limit results to only tags of interest and oldest tickets first to avoid hitting script processing time limits
- Limited assignment to a maximum of 10 tickets per pass to avoid the entire queue from being assigned at once
- Fixed an issue where the last agent assigned would continue to be pushed tickets if there were no active agents including the previously assigned agent

This script builds off the work done here: https://support.zendesk.com/hc/en-us/community/posts/203458976-Round-robin-ticket-assignment

Agent Status will automatically be set to `yes` when:
- It is a week day. A weekday is defined as Monday 0:00 to Friday 23:59:59 in Eastern Time. This will be adjusted for DST based on the value in the configuration spreadsheet. (Note: Since a day is based on an ET day, agents working outside of ET have their days defined a little differently. However, this is only impactful if their working hours are within three hours (the offset from ET) of the day boundary. Ex: If the script runs at Sunday 22:00 PT, it will think it is Monday 2:00. If the agent working hour includes 2:00, they will be assigned tickets a few hours early. Currently, our PT agents work 9-17PT so they are not affected by this.)
- And, it is within the agent's working hours. Working hours are defined in UTC. Working hours will be automatically adjusted for DST based on the value in the configuration spreadsheet.

## Usage

0. Open up the target spreadsheet and click Tools > Script Editor.
0. Copy and paste the script in there.
0. Replace 'SHEET_ID' with your spreadsheet id in the `setConfiguration` method. This can be found from the URL https://docs.google.com/spreadsheets/d/SPREADSHEET_ID/edit?ts=123#gid=456
0. Configure a trigger to run the script automatically.
    - Open up the script editor and click Edit > Current Project's Triggers > Add a new trigger
    - Select the following options: main, Time-driven, Minutes timer, Every 30 minutes.
0. Set up your spreadsheet sheets and columns based on the examples in `agents.csv` and `configuration.csv`

## Configuration

Configuration is done through an additional sheet in your spreadsheet. The sheet must be named "Configuration". An example of the rows and columns expected is provided in `configuration.csv`. The rows and columns must match for this script to work (Ex: subdomain is always in cell B1).

The following fields are configurable through the configuration spreadsheet:

- __subdomain:__ The domain of your zendesk account
- __username:__ The username of your zendesk account that we will be connecting as
- __token:__ The token we will use to connect to your zendesk account
- __working hours:__ Used to populate the working hours dropdown.
This should be the start and end of working hours in UTC and 24 hour notation separated by a dash.
You'll need to convert from your local time zone into UTC time zone. For example, 9am - 5pm EST would be represented as `14-22` and 9:00AM - 5:00PM PST would be `17-1`.
- __daylight savings time?:__ Determine if we should compensate for daylight savings time. If this flag is set incorrectly, all calculations will be off by one. Value should be `yes` or `no`. For an example converter, check out https://www.timeanddate.com/worldclock/converter.html?iso=20180116T140000&p1=179&p2=1440

## References
- Google Apps Sheets API: https://developers.google.com/apps-script/reference/spreadsheet/

- Triggers https://developers.google.com/apps-script/guides/triggers/
- API Console https://developer.zendesk.com/requests/new
