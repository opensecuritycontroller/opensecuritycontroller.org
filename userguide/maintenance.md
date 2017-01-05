## Maintaining OSC
If you have admin rights in the OSC web application, you can perform maintenance tasks such as
upgrading the OSC virtual application to a later version, backing up the OSC database, and restoring a
backed-up database


### Creating a Database Backup
> Before you begin
> You have admin rights in the OSC web application.

As a precaution, you can back up the OSC database at regular intervals. Currently, you cannot schedule automatic backups. 

1. In the OSC web application, select **Manage | Server | Maintenance.** ...
2. ...
TBD: Adding link to the troubleshooting an upgrage or restore page.

### Restoring a Database Backup
> Before you begin
> * Confirm you have admin rights in the OSC web application.
> * The backed up file is accessible through your client.
> * The OSC version when you backed up the database and the current OSC version are same.

You can revert the OSC database to an earlier state by uploading...

### Upgrading OSC
> Before you begin
> * Confirm you have admin rights in the OSC web application.
> * As a precaution, back up the OSC database.
> * You have the image bundle (.zip) of the version you want to upgrade to.

1. In the OSC web application, select **Manage | Server | Maintenance**.
The **Maintenance** page displays....

### Configuring a Notification Email
Whenever there is a failure, if an alarm is configured, an email notification is sent to the email ID configured. You can setup email notification to be sent for failures that require immediate attention. For the email notifications to be sent, you have to first define an SMTP server.
...

### Download OSC Logs
You can download the OSC log files to troubleshoot an issue or provide the logs to Support. You can also include the database backup in the support bundle.