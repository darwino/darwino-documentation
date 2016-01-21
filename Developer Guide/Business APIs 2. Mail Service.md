#Mail Service

The Mail Service is a basic interface for sending emails. There is currently no REST service support for the Mail Service, and it is not supported on mobile.

To send a simple email, create a MailMessage object and set the mail parts via the MailMessage methods, then call send().

```
MailService mailService = Platform.getService(MailService.class);

MailMessage m = new MailMessage();
m.setFrom("playground@darwino.com");
m.setTo("darwinounit2@gmail.com");
m.setSubject("Simple email");
m.setContentText("This email is a simple one");
mailService.send(m);
```

HTML body content can be created via the setContentHTML() method:
```
m.setContentHTML(“Here is <b>bold</b> and <i>italic</i>.”);
```

To send more complicated messages, the MailMimePart class allows the creation of MIME content from text:
```
MailMimePart ht = new MailMimePart();
ht.setContent(new TextContent("Alternate <b>HTML</b> email representation",TextContent.UTF_ENCODING,HttpBase.MIME_HTML));
```


Attachments are also supported:
```
MailMimePart at = new MailMimePart();
at.setContent(new TextContent("This one is <b>HTML</b>",TextContent.UTF_ENCODING,HttpBase.MIME_HTML));
at.setName("Attachment.html");
m.addMimePart(at);
```

To send images, pass the image string as BASE64 to setContent() and supply a filename for the attachment:
 
```
MailMimePart at1 = new MailMimePart();
String IMAGE = "R0lGODdhAAGAAKIAAP38+/3h3cjN5P3HwgAAAP8AoP8AGv8EIywAAAAAAAGAAAAD.....";
at1.setContent(new Base64Content(IMAGE,HttpBase.MIME_IMAGE_PNG));
at1.setName("Attachment.png");
m.addMimePart(at1);
```

