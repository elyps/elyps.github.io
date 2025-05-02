## Record information

Here is the recommended configuration to produce restrictive records. This will advise receiving mail servers to drop all incoming email sent from your domain.

Reporting email addresses

Emails to send aggregate feedback reports (optional)


## Record preview

| Type | Name                | Content                                           | TTL  |
| ---- | ------------------- | ------------------------------------------------- | ---- |
| TXT  | bastian-fischer.dev | "v=spf1 -all"                                     | Auto |
| TXT  | *._domainkey        | "v=DKIM1; p="                                     | Auto |
| TXT  | _dmarc              | "v=DMARC1; p=reject; sp=reject; adkim=s; aspf=s;" | Auto |






