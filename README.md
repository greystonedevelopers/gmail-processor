# gmail-processor

# gmail-processor for the [Go-guerrilla](https://github.com/flashmob/go-guerrilla) package.

## About

This package is a _Processor_ for the Go-Guerrilla default backend implementation. Use for this in your project
if you are using Go-Guerrilla as a package and you would like to add the ability to deliver emails 
via gmail API's using Go-Guerrilla's default backend.

## Usage

Import `"github.com/flashmob/maildir-processor"` or `github.com/phires/go-guerrilla` to your Go-guerrilla project.
Assuming you have imported the go-guerrilla package already, and all dependencies.

Then, when [using go-guerrilla as a package](https://github.com/flashmob/go-guerrilla/wiki/Using-as-a-package), do something like this

```json

{
  "log_file" : "",
  "log_level" : "debug",
  "pid_file" : "smtptogooglebridge.pid",
  "allowed_hosts": ["*"],
  "backend_config" :
  {
    "log_received_mails" : true,
    "save_process": "HeadersParser|Header|Hasher|Gmail|Debugger",
    "save_workers_size":  3,
    "sa_key" : "gsgmailapi-9e49eeb1e33c.json",
    "mail_sender": "printer@domain.co.za",
    "mail_scopes" : "https://mail.google.com/",
    "use_original_mail": false,
    "additional_body_message": "Please find your attachment",
    "save_debug_mails": false
  },
  "servers" : [
    {
      "is_enabled" : true,
      "host_name":"192.168.1.224",
      "max_size": 20971520,
      "private_key_file":"config_test.go",
      "public_key_file":"config_test.go",
      "timeout":160,
      "listen_interface":"192.168.1.224:2526",
      "start_tls_on":false,
      "tls_always_on":false,
      "max_clients": 10
    }
  ]
}
```


```go

func (b *Bridge) Start() {
    b.smtpServer = guerrilla.Daemon{}
    config, errConfig := b.smtpServer.LoadConfig(smtpConfigFile)
    if errConfig != nil {
        panic(errConfig)
    }
    b.smtpServer.AddProcessor("Gmail", gmailprocessor.Processor)
    err := b.smtpServer.Start()
    if err == nil {
        println("Server Started!")
    } else {
    panic(err)
    }
    for _, host := range config.AllowedHosts {
        b.smtpServer.Logger.Info("Allowed Host: ", host)
    }
	
	// you need to keep the main thread alive etc.
}

```

See the configuration section for how to configure.


## Configuration

The following values are required in your `backend_config` section of your JSON configuration file

#### Config Options: set in the backend_config section
    
    -   sa_key - type string Value - gsgmailapi-9e49eeb1e33c.json  (after you create your service account download the file and supply it)
    -   mail_sender - type string - use this as the mail account you want to send from in google suite.
						            eg printer@<googleapi-domain>.co.za
					                make sure in the google admin under the security you sent the "domain wide deligation"
					                client account and scope permissions
	-   mail_scopes - type string - set this as your scopes list see point above.
							        example https://mail.google.com/ defined in // import "google.golang.org/api/gmail/v1
    -   save_process - type string - backend processes to call example -  HeadersParser|Header|Hasher|Gmail|Debugger
    -   use_original_mail type bool - use this option to send the raw data straight to google. 
                                    if false you have the ability to manipluate aything on the message
    -   additional_body_message - type string - Basic message body to insert if you need one. if the above bool is false
    -   save_debug_mails - type bool - use this to dump original and revised email to disk.


## Example

````
Starting SMTP Bridge vDEVELOPMENT
time="2025-05-21T11:20:04+02:00" level=info msg="Loaded credentials from file: gsgmailapi-9e49eeb1e33c.json"
time="2025-05-21T11:20:04+02:00" level=info msg="Loaded credentials from file: gsgmailapi-9e49eeb1e33c.json"
time="2025-05-21T11:20:04+02:00" level=info msg="Loaded credentials from file: gsgmailapi-9e49eeb1e33c.json"
time="2025-05-21T11:20:04+02:00" level=info msg="pid_file (smtptogooglebridge.pid) written with pid:170451"
time="2025-05-21T11:20:04+02:00" level=debug msg="making servers"
time="2025-05-21T11:20:04+02:00" level=info msg="Starting: 192.168.1.224:2526"
time="2025-05-21T11:20:04+02:00" level=info msg="processing worker started (#2)"
time="2025-05-21T11:20:04+02:00" level=info msg="processing worker started (#1)"
time="2025-05-21T11:20:04+02:00" level=info msg="processing worker started (#3)"
time="2025-05-21T11:20:04+02:00" level=info msg="Listening on TCP 192.168.1.224:2526"
time="2025-05-21T11:20:04+02:00" level=debug msg="[192.168.1.224:2526] Waiting for a new client. Next Client ID: 1"
time="2025-05-21T11:20:04+02:00" level=info msg="main log configured to stderr"
Server Started!
time="2025-05-21T11:20:04+02:00" level=info msg="Allowed Host: *"
time="2025-05-21T11:23:20+02:00" level=debug msg="[192.168.1.224:2526] Waiting for a new client. Next Client ID: 2"
time="2025-05-21T11:23:20+02:00" level=info msg="Handle client [192.168.1.10], id: 1"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n220 192.168.1.224 SMTP Guerrilla(unknown) #1 (1) 2025-05-21T11:23:20+02:00\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: EHLO [192.168.1.10]"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250-192.168.1.224 Hello\r\n250-SIZE 20971520\r\n250-PIPELINING\r\n250-ENHANCEDSTATUSCODES\r\n250 HELP\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: MAIL FROM:<dev@domain.local>"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250 2.1.0 OK\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: RCPT TO:<user@gmail.com>"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250 2.1.5 OK\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: RCPT TO:<user1@domain.co.za>"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250 2.1.5 OK\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: RCPT TO:<user2@domain.co.za>"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250 2.1.5 OK\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: RCPT TO:<user3@domain.co.za>"
time="2025-05-21T11:23:20+02:00" level=debug msg="Writing response to client: \n250 2.1.5 OK\r\n"
time="2025-05-21T11:23:20+02:00" level=debug msg="Client sent: DATA"
time="2025-05-21T11:23:24+02:00" level=debug msg="Writing response to client: \n354 Enter message, ending with '.' on a line by itself\r\n"
time="2025-05-21T11:23:33+02:00" level=info msg="Hash: d1515a4d02532c07d7b31a852916eb1d "
time="2025-05-21T11:23:40+02:00" level=info msg="Headers are:map[Cc:[user1@domain.co.za, user2@domain.co.za] Content-Type:[multipart/mixed; boundary=\"=-j77npLw+HtpGSWV38xgH\"] Date:[Wed, 21 May 2025 11:23:20 +0200] From:[dev <user@domain.local>] Message-Id:[<a60c2d8147b23aeae09d2f7dcdda94765dedacb0.camel@domain.local>] Mime-Version:[1.0] Subject:[Test from SMTP to google bridge] To:[User <user@gmail.com>, ToUser <user@domain.co.za>] User-Agent:[Evolution 3.52.3-0ubuntu1]]"
time="2025-05-21T11:23:40+02:00" level=debug msg="Writing response to client: \n250 2.0.0 OK: queued as d1515a4d02532c07d7b31a852916eb1d\r\n"
time="2025-05-21T11:23:40+02:00" level=debug msg="Client sent: QUIT"
time="2025-05-21T11:23:40+02:00" level=debug msg="Writing response to client: \n221 2.0.0 Bye\r\n"
````


## Credits

This package depends on phires [Go phires](https://github.com/phires/go-guerrilla) package.
