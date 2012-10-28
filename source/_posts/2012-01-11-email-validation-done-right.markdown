---
comments: true
date: 2012-01-11 17:48:24
layout: post
slug: email-validation-done-right
title: Email validation done right
wordpress_id: 289
categories: programming
tags: email validation
keywords: email validation regex regexp
---

Let's imagine that you have to check if a string is a valid email. You could come up with something like:
``` ruby
/[a-zA-Z0-9\.]+@[a-z]+\.[a-z]+/
```
    
    



It works, right? WRONG. Sure it'll handle a couple of your test examples. But it's not ready for real world usage. Here's a standards compliant Perl regex.

``` ruby
/(?(DEFINE)
   (?<address>         (?&mailbox;) | (?&group;))
   (?<mailbox>         (?&name;_addr) | (?&addr;_spec))
   (?<name_addr>       (?&display;_name)? (?&angle;_addr))
   (?<angle_addr>      (?&CFWS;)? < (?&addr;_spec) > (?&CFWS;)?)
   (?<group>           (?&display;_name) : (?:(?&mailbox;_list) | (?&CFWS;))? ;
                                          (?&CFWS;)?)
   (?<display_name>    (?&phrase;))
   (?<mailbox_list>    (?&mailbox;) (?: , (?&mailbox;))*)

   (?<addr_spec>       (?&local;_part) \@ (?&domain;))
   (?<local_part>      (?&dot;_atom) | (?&quoted;_string))
   (?<domain>          (?&dot;_atom) | (?&domain;_literal))
   (?<domain_literal>  (?&CFWS;)? \[ (?: (?&FWS;)? (?&dcontent;))* (?&FWS;)?
                                 \] (?&CFWS;)?)
   (?<dcontent>        (?&dtext;) | (?&quoted;_pair))
   (?<dtext>           (?&NO;_WS_CTL) | [\x21-\x5a\x5e-\x7e])

   (?<atext>           (?&ALPHA;) | (?&DIGIT;) | [!#\$%&'*+-/=?^_`{|}~])
   (?<atom>            (?&CFWS;)? (?&atext;)+ (?&CFWS;)?)
   (?<dot_atom>        (?&CFWS;)? (?&dot;_atom_text) (?&CFWS;)?)
   (?<dot_atom_text>   (?&atext;)+ (?: \. (?&atext;)+)*)

   (?<text>            [\x01-\x09\x0b\x0c\x0e-\x7f])
   (?<quoted_pair>     \\ (?&text;))

   (?<qtext>           (?&NO;_WS_CTL) | [\x21\x23-\x5b\x5d-\x7e])
   (?<qcontent>        (?&qtext;) | (?&quoted;_pair))
   (?<quoted_string>   (?&CFWS;)? (?&DQUOTE;) (?:(?&FWS;)? (?&qcontent;))*
                        (?&FWS;)? (?&DQUOTE;) (?&CFWS;)?)

   (?<word>            (?&atom;) | (?&quoted;_string))
   (?<phrase>          (?&word;)+)

   # Folding white space
   (?<fws>             (?: (?&WSP;)* (?&CRLF;))? (?&WSP;)+)
   (?<ctext>           (?&NO;_WS_CTL) | [\x21-\x27\x2a-\x5b\x5d-\x7e])
   (?<ccontent>        (?&ctext;) | (?&quoted;_pair) | (?&comment;))
   (?<comment>         \( (?: (?&FWS;)? (?&ccontent;))* (?&FWS;)? \) )
   (?<cfws>            (?: (?&FWS;)? (?&comment;))*
                       (?: (?:(?&FWS;)? (?&comment;)) | (?&FWS;)))

   # No whitespace control
   (?<no_ws_ctl>       [\x01-\x08\x0b\x0c\x0e-\x1f\x7f])

   (?<alpha>           [A-Za-z])
   (?<digit>           [0-9])
   (?<crlf>            \x0d \x0a)
   (?<dquote>          ")
   (?<wsp>             [\x20\x09])
 )

 (?&address;)/x
```

 I couldn't even imagine that the matter is *this* complex.
