# ACME Working Group meeting at IETF 100
Thursday, November 16 at 15:50 local time (7:50 UTC)

## Status update
| Who | Slides | A/V |
| ----|--------|-----|
| Chairs | [chair-slides](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-chair-slides/) |  [link](https://youtu.be/n_pUc9XfjWs?t=127) |

- Published acme-08;
  - OPS and GENART reviews done;
- New versions for all new drafts.

## Email
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Alexey | [extensions-to-acme-for-email](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-extensions-to-acme-for-email/) | [link](https://youtu.be/n_pUc9XfjWs?t=313) | [ietf-acme-email-tls-02](https://tools.ietf.org/html/draft-ietf-acme-email-tls-02) & [ietf-acme-email-smime-01](https://tools.ietf.org/html/draft-ietf-acme-email-smime-01) |

### draft-ietf-acme-email-tls-02
TLS certificates for email services.

See slides (#2) for a summary of the changes since Prague.

Open issues:
 - Include port and service (e.g., smtp, imaps) in challange hash?

MT: Port under the hash: OK.  Are you suggesting or even requiring that the ACME server insists on a particular port number for these protocols?

AM: in theory you could put any of these on a non standard port.  I should probably say: CA may enforce that port number correlates to the service.

MT: Ability to run TCP server on my machine on port 5000 is not a good indication that person controls the machine.  Definitely want to have some text around this and maybe strongly recommend there be some policy at the CA that says: if the port number is different from what I say here then not a good idea to accept this.  We did the same thing for HTTP.  

RB: The analogy MT was pointing at is the HTTP challenge: the CA will only connect to the validation server on port 80, which is supposedly privileged.  Use the standard port for the services we are talking about.

AM: OK.

 - Cover both TLS and non TLS ports (start plain, try STARTTLS and upgrade to TLS if available, e.g.: IMAP)?  In theory same certificate could be used for both ports, in practice services and ports are different.  Therefore difficult to get a certificate that covers both in one go.

RB: Does not seem like an ACME issue: if certificate you are issuing is agnostic as to port number it should be fine.  Only if using an SRV name or specifying the port number in the certificate then you need some binding.

AM: Yes, I was thinking whether it is possible to get SAN and SRV name in the same certificate in one go.  The answer is probably no.  It is an optimisation, I don't think it matters in practice.

JL: IMAP server I am using I don't think it has provision for doing separate certficates for the two services, so combining is important.

JL: Unrelated question: any reason why POP is missing?

AM: No particular reason.

JL: Tiny data point: I use it.  Anything that applies to IMAP equally applies for POP.

AM: It'd need new capability challenge.  Only DNS challenge would work out of the box.

JL: Adding cap challenge should be same effort as adding it to IMAP.

AM: OK will do.

 - Support LMTP?
 
AM: A variant of SMTP for final delivery, some of these services are public facing.  Trouble is LMTP doesn't have registered port number, and neither is the lmtp name registered.  Could do registration in a separate document, no problem.

Next steps:
 - More reviews (Richard should review);
 - Call for implementers, could be something in London on top of letsencrypt for example.

### draft-ietf-acme-email-smime-01
Certificates for S/MIME users.

AM: Still rather raw.  Done some changes but really need to flash out how the messages are going to look like.

See slides (#5) for a summary of the changes since Prague.

RB: one thing not clear from the document is whether you are trying to verify someone is able to receive mail at the claimed address, or send email from that address, or both?

AM: open question, but I suspect the answer is both (and sending is more powerful assertion than receiving.)

JL: just being able to receive is not sufficient: I could probably get an ACME certificate for every IETF mailing list... sending is problematic as well.  Probably need text in security considerations saying that a "certain amount" of spoofing of identities is unavoidable.

AM: right.

HN: Same point as JL.  For receiving OK.  For sending we need to make sure an email validation system like DMARC is mentioned in the document.

AM: OK, sounds good.

RB: I should observe that verification of receipt is used in typical web based workflows where email is sent to an address with a web link to click on for verification.  I don't know if it's evidence of sufficiency but it's evidence that this works in some other cases.

JL: (not intelligible)
  
RB: Last observation: looking at JP here as there seems to be some overlap between this and the empirical telephone number verification case?

JP: Return routability tests of various kinds have been used for some time - stolen from practices used for email.  If the endpoint is smart enough you could add more on top to strengthen the verification (e.g., crypto in the SIM card), or identifiers (IMEI, IMSI) that are maintained by the network therefore the network can be queried.  If we want to make this more generic by defining a profile that encompasses both return routability tests, I'd be willing to work with you (Alexey) on that, if you are interested.

AM: Sure.

See slides (#6) for open issues.

## Main document wrap-up
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Richard | [acme-acme](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-acme/) | [link](https://youtu.be/n_pUc9XfjWs?t=1159) | [ietf-acme-acme-08](https://tools.ietf.org/html/draft-ietf-acme-acme-08) |

## Telephony
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Mary | [token-identifier-and-challenges](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-token-identifier-and-challenges/) | [link](https://youtu.be/n_pUc9XfjWs?t=3669) | [barnes-acme-token-challenge-00](https://tools.ietf.org/html/draft-barnes-acme-token-challenge-00) & [barnes-acme-service-provider-code-00](https://tools.ietf.org/html/draft-barnes-acme-service-provider-code-00) & [ietf-acme-service-provider-02](https://tools.ietf.org/html/draft-ietf-acme-service-provider-02) |
| Jon | [acme-stir](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-stir/) | [link](https://youtu.be/n_pUc9XfjWs?t=3050) | [peterson-acme-authority-token-00](https://tools.ietf.org/html/draft-peterson-acme-authority-token-00) & [ietf-acme-telephone-01](https://tools.ietf.org/html/draft-ietf-acme-telephone-01) |

## STAR
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Thomas | [acme-star](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-star/) | [link](https://youtu.be/n_pUc9XfjWs?t=4767) | [ietf-acme-star-01](https://tools.ietf.org/html/draft-ietf-acme-star-01) |

