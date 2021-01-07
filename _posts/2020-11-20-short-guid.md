---
title: Short Guid
author: PipisCrew
date: 2020-11-20
categories: [.net]
toc: true
---

https://www.singular.co.nz/2007/12/shortguid-a-shorter-and-url-friendly-guid-in-c-sharp/
https://github.com/csharpvitamins/CSharpVitamins.ShortGuid/blob/d25a2215e0b35f600efb2bbc178d1533122c8ea9/CSharpVitamins.ShortGuid/ShortGuid.cs#L126
https://www.nuget.org/packages/CSharpVitamins.ShortGuid/

* * *

```js
private string GenerateShortGUID()
{
	string enc = Convert.ToBase64String(Guid.NewGuid().ToByteArray());
	enc = enc.Replace("/", "_");
	enc = enc.Replace("+", "-");
	return enc.Substring(0, 22);
}
```

* * *

Mads Kristensen flavor (convert GUID to base64)
```js
// src - https://www.madskristensen.net/blog/A-shorter-and-URL-friendly-GUID
// ref - https://stackoverflow.com/a/8118804
using System;

public static class GuidEncoder
{
 public static string Encode(string guidText)
 {
  Guid guid = new Guid(guidText);
  return Encode(guid);
 }

 public static string Encode(Guid guid)
 {
  string enc = Convert.ToBase64String(guid.ToByteArray());
  enc = enc.Replace("/", "_");
  enc = enc.Replace("+", "-");
  return enc.Substring(0, 22);
 }

 public static Guid Decode(string encoded)
 {
  encoded = encoded.Replace("_", "/");
  encoded = encoded.Replace("-", "+");
  byte[] buffer = Convert.FromBase64String(encoded + "==");
  return new Guid(buffer);
 }
}
```

origin - https://www.pipiscrew.com/?p=19381 short-guid