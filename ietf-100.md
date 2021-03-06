# ACME Working Group meeting at IETF 100
Thursday, November 16 at 15:50 local time (7:50 UTC)

## Status update
| Who | Slides | A/V |
| ----|--------|-----|
| Chairs | [chair-slides](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-chair-slides/) |  [link](https://youtu.be/n_pUc9XfjWs?t=127) |

- Published acme-08;
  - OPS and Gen-ART reviews;
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

AM: Still rather raw.  Done some changes but really need to flesh out how the messages are going to look like.

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

On proactive issuance, i.e., when to send the CSR?  (See slides for details about requirements and alternative flows.)

RB: One approach is to always send the CSR twice (PR#342).  Simple logic (no branching), but unnecessary caching on client side if client always talk to a CA that caches the CSR + retransmit cost.

(?): How do you know the two CSRs are the same?

RB: This issue always exists when you send it twice

EKR: Maybe... (long argument which I could not get.)

RB: The other approach is CA signals client whether to do the second transmission.  Another way you can view this dicothomy is whether the first and second requests are part of the same flow, or decoupled things.  The first request is more or less a stateful thing that just gets advice and the second one (when you need a second one) as the actual issuance transaction.  The more decoupled case that EKR was mentioning just now has the benefit that you only spend as much caching and bandwidth as you need to for a given CA and transaction - you don't cache the CSR if you don't need to.  As EKR was mentioning you get this decoupling where there doesn't need to be any coupling between the first and the second requests and so you can have a slightly simpler logic on the server by just considering each request on its own without having to bind sequence of requests together.  In this case the logic on the client is a little more complicated because it has to switch between the two cases.

JL: why do you need the first CSR?

RB: one of the use cases for getting the CSR upfront is that of legacy APIs that you might have behind an ACME interface that require the CSR at the beginning of the process.  In this case first and second CSR are required to be the same because the backend has done everything based on the first CSR.

RB: The question I have for the working group is which of these two approaches do we prefer?  Had some discussion already on the list on PR#342.  Personally I prefer PR#350 because it keeps things a little more decoupled and avoids unnecessary retransmits, but not a strong opinion.

JHA: strongly prefer the former (PR#342) or, should I say, strongly disprefer the latter (PR#350).  Main reason: I want to avoid that "this CA does something this way and that CA does something that way"; the risk is proliferation of clients that only implement one behaviour and not the other and then we don't have an interoperable ecosystem.  Prefer simple and straightforward solution.

EKR: not a crazy concern, but this is what interop testing is for, and actually having sensible protocol hygiene is more important. And what the security requirements are in term of what the CA needs to check between the two CSRs?

JHA: CA can hash the two and make sure the hash is the same.

EKR: That means you think they have to be the same, why do they have to be the same?

JHA: I don't think this is necessarily true.

EKR: This is concerning.

JHA: In our setup and policy it's OK if they are different.  I proposed hash equality because I thought you wanted them to be identical, but if you don't think so, I don't either.

RB: There are cases (legacy API) where the CA requires them to be identical.

RB: If you do the checks before issuance on the second one then it doesn't matter what the first one was...

EKR: yes, but only as long as the protocol says you have to issue off the second one.

RB: yeah.  you say you do all your pre-issuance checks on the 2nd one.

EKR: but again, protocol has to say if they are the same or not.  I see great opportunities for screwing things up in a bunch of different ways.

JHA: I agree.  Would you be happy with PR#342 if we said the in the 1st CSR the public key and extensions are ignored and the only that matter is the 2nd one?

EKR: I'd be happy-er

RB: we could say: whatever checks the CA does on an issuance request have to be done with regard to the CSR that is sent in the 2nd request?

EKR: no

MT: what aspects of the CSR are needed in order to tell the client what to do?  It's only the identifiers, right?

RB: issue with legacy APIs that need a real CSR.

EKR: why unify the two flows?  Can't this be separated in two different flows with the server advertising which one to follow?

JHA: I think only one of the flows would get implemented.  There are 50-100 ACME clients which are essentially letsencrypt clients... how to build the protocol so that clients have an incentive to work with everyone?

EKR: (summary) this seems the wrong way to go

JHA: ideally, first round send just the identifiers, second round send the full CSR.  we'd need to hear from CAs that have an API that needs CSR first and would have trouble adopting ACME without this flow.

EKR: it seems you and I prefer the same thing (PR#342).  I still need to be convinced we need "CSR first" flow.

RS (no hat): could we make the error code generic so whenever you get this error code, resubmit with the CSR and if the CSR is resent it MUST be the same one?

JHA: could work

RB: need to see the PR to understand exactly how it'd work

RB: surprised that PR#342 works with LE workflows with wildcards?

JHA: the identifier notion introduced in PR#342 is a little different from the identifiers in the authorisation objects.

RB: so basically in PR#342 you'd take the collection of DNS name SANs, translate them to identifier objects and send them in?
JHA: yes

MT: I like PR#342 as well, an editorial note: be more precise the definition of identifiers (e.g., define what it means to have a wildcard ID and the format of it)

RB: there seems to be a bit of mass behind PR#342.  Having a signal in the directory object to take care of legacy whereas the the core ACME is focused on going forward use cases.  Then clients can implement the legacy affordance or not.

YN: let's take a hum.  Proposed way forward is accept PR#342.  Hum if you understand PR#342.

room: huuuuum

YN: Hum if you don't

room: (silence)

YN: hum if you agree this is the right way forward

room: hum

YN: hum if you don't agree this is the right way forward

room: (silence)

RB: I'll work with Daniel to get PR#342 landed and talk with folks who have legacy APIs and see if we need to add some affordance for backwards compat.

RB: Have this done and review comments incorporated by early December.

RS: Kathleen, assuming this is the only substantive technical issue, do you want us to do another WGLC?  Or just move it forward after like a week?

KM (AD): as in TLS, do a narrowly focused WGLC on only areas of question, not the whole thing.  Would be nice to have this done soon so that it doesn't need another AD review.  The sooner the better.

RB: Hope to get this done well before March.

## Telephony
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Jon | [acme-stir](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-stir/) | [link](https://youtu.be/n_pUc9XfjWs?t=3050) | [peterson-acme-authority-token-00](https://tools.ietf.org/html/draft-peterson-acme-authority-token-00) & [ietf-acme-telephone-01](https://tools.ietf.org/html/draft-ietf-acme-telephone-01) |

JP: both I and Mary are presenting basically the same material and trying to pose the same questions.  Trying to cover both as much as I can.

JP: in STIR we have defined certificate profiles and a certain number of certificate extension.  Two branches: TN (telephone numbers, maybe associated with an end-user) and SPC (service provider codes).  It'd be very difficult, maybe impossible to administer those without something like ACME.

See slides (#3-7) for a description of how STIR works, and how STIR plans to make use of ACME, and what the alternatives to prove identities are.

JP: Generalising off the telephone use cases: an ACME server has a preexisting relationship with an identity authority of some kind; when a user claims a certain identity, the ACME server can challenge the user asking it to go to the authority that controls the relevant name space and get a token; if the authority thinks user is legit, they will give back something cryptographically signed that the user can present to the ACME server; if the ACME server verifies that "something" it issues the user a new certificate.

JP: Other possible use case: E-ZPass / FasTrack with license plate authority.

JP: Therefore define a generic token, with an explicit type governed by some registry and some more things in it (like a hint).

JP: Mary's approach is slightly different, we want to check with the ACME WG whether this is a good idea, and which way is preferable.

(?): clarifying question: you sort of imply ACME service reaching out to the authority to get the token?

JP: no

| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Mary | [token-identifier-and-challenges](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-token-identifier-and-challenges/) | [link](https://youtu.be/n_pUc9XfjWs?t=3669) | [barnes-acme-token-challenge-00](https://tools.ietf.org/html/draft-barnes-acme-token-challenge-00) & [barnes-acme-service-provider-code-00](https://tools.ietf.org/html/draft-barnes-acme-service-provider-code-00) & [ietf-acme-service-provider-02](https://tools.ietf.org/html/draft-ietf-acme-service-provider-02) |

See slides (#2-?) for an overview.

MB: (audio breaks lots)

JP: I'm arguing for a model with sub-typing and an IANA registry that specifies how these tokens work in particular.  The approach in Mary's draft is more focused on identifying that entity designation than on something that could potentially use different forms of tokens.

CW: Jon covered it pretty well.  Service provider corresponds to the SHAKEN stuff that we've done in the STIR domain in terms of the service provider code.  If I understand Jon's proposal, the difference is that instead of having a predefined entity code, we have a type that is defined in a profile document, or something like that, and that characterises all the claims and other things.  My personal opinion is I'm fine with it.  Wish Mary was here to give her position as well.

MB: I have two drafts, one generic and another which provides an example of using the former.  And in this model you woudn't need the registration and is therefore lighter weight in my opinion.  It doesn't put as much knowledge of how we're doing this in the ACME server.  So, it's totally up to the authority and its relationship with the end-entity to figure out the value of the token and the unique identifier.

JP: yes, I guess it's lighter weight but since we have to assume a pre-existing association between the authority and the CA anyway, being lighter weight limits the kind of things you can end up talking about.

MB: I see your point.  I was doing it in the original service provider code document, where the perspective was that the PA and CA already have that trust relationship and in the SHAKEN model we absolutely have that.  We have a more restricted model.  I can see your point, OK.

JP: I think this can be useful in lots of cases in ACME, but this is a question for the room, that's what I'm interested to hear about.

MB: it doesn't cost anything to not be generic.  My concern is if we are too generic we are going to get use cases that we need to evaluate and make sure it works for those situations.  People have already implemented the SKAKEN stuff and it works...

RB: I think this is worth doing generically.  I've had a similar use case in mind for DNS names for a while.  Wouldn't it be nice even for the DV case if the CA could get information directly from a registry as to who holds which domain name?  It's worth at least including that case.  The thing we need to struggle with here is what the right axes for generalisation are.  The only draft I read is Jon's and it looks roughly correct to me in terms of branching on token type.  I'm broadly supportive of this approach.

CW: I agree it'd be good to have a generic approach.  One thing we could do is having sort of a default mode where there is e.g, an UUID.  I can see a scenario where 80-90% of the folks using this may have pretty much the same mechanism: you don't have to necessarily define every single use case - may be something to consider.

RS: we already adopted both.  If they are converging, authors should get together and merge.

JP: if we merge Mary's and mine proposal, both the service provider draft and the TN document would then rely on the generic one.  And I'm happy to put in Chris's UUID case as I think it makes a lot of sense.  I love the DV case.  I think it's a win-win.

RS: Mary opinions?

MB: Jon, Chris and I will work together.

TH: I generally like the mechanism you guys are converging on.  A slight thing on the amount of domain knowledge that you need and I think that you still require some amount of it in the CA to know something about (let's call it) the number space that you are eventually going to issue the certificate for, because I don't think you can get away from the fact that in STIR a wildcard means something and in regular domain name FQDNs it means another thing.  Let's just try and not be so generic that we expect every CA to be able to have the knowledge to issue certificate of fundamentally different types.

RB: just to clarify the context: the context is within an authorisation transaction at which point you have already specified this is the specific identifier you are validating.  So, I don't think we run into the questions of wildcards, etc.

TH: I agree, I'm just trying to say that the amount of state can only get so low, because you are going to be able to match it to what kind of identifier is going to be issued.  There are some of these identifiers which are functionally issued with only one reference authority and the amount of state you are willing to take is whatever the reference authority requires.

CW: you also have to assume that the ACME implementation needs to support STIR certificates as well so there are a couple of levels of qualification that have to happen before you can actually give a certificate.

YN (chair): guidance is talk together and come back with a single submission and then we can call for adoption, hopefully before London.

JP: OK

RS (chair): do we need to hum on that? Is everyone fine?  If the charter has to be adjusted we'll deal with that.  Idea is to have this as a WG doc before London.

RB: there shouldn't be any need for charter adjustments.

RS: It depends on how generic you guys make it.

## STAR
| Who | Slides | A/V | Draft(s) |
| ----|--------|-----|----------|
| Thomas | [acme-star](https://datatracker.ietf.org/meeting/100/materials/slides-100-acme-acme-star/) | [link](https://youtu.be/n_pUc9XfjWs?t=4767) | [ietf-acme-star-01](https://tools.ietf.org/html/draft-ietf-acme-star-01) |

See slides (#3-8) for a summary of the changes since Prague.

JP: with my use case which is not domain names.  I do appreciate the de-lurkage that you did thus far.  The term DNO, and the notion that there is a domain name owner that cancels an automatic renewal still doesn't fit the language of my use case, but I'd love doing exactly what you are doing.  So, just a little bit less specificy of the domain name part of this and STIR would totally use this.  Happy to help.

TF: OK, thanks.

TF: interaction with certificate transparency is TBD.  Diego went to the TRANS WG session to present the problem.

DL: No conclusive answer whether this would pose a problem to CT or not.  What I've heard from Melinda was they are OK for documenting the problem and solution space.  What is not completely clear is where this work would be done since TRANS may be closing down in the next few  months.

DL: If others that were there (EKR?) could share their impression as well?  

EKR: AD hat off.  Given clock skews forces short-term to be not below 7-days-ish, the increase in ingestion rate should be fine. So, no actual problem here, you should just go forward.

RB: CT shall arrive at some solution for short-lived certificates anyway, regardless of this document, because there are multiple different fronts from which this is coming.  So it seems it's their problem, not a problem for this document to solve.

RB: One quick question out of curiosity: any CAs interested in deploying this?

TF: Yes, Digicert contacted us; they said they have "short" as short as 8 hours.
