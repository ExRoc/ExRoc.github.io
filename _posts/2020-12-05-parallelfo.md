---
title: Parallel.ForEach
author: PipisCrew
date: 2020-12-05
categories: [.net]
toc: true
---

```js
Parallel.ForEach(dTCustomers.AsEnumerable(), new ParallelOptions { MaxDegreeOfParallelism = 5 }, dr =>
{
	string url = dr["url"].ToString();

	WebClient web = new WebClient();
	web.Encoding = Encoding.UTF8;
	Console.WriteLine(web.DownloadString(url));
}
```

https://www.c-sharpcorner.com/UploadFile/efa3cf/parallel-foreach-vs-foreach-loop-in-C-Sharp/

--

[Multithreading made easy – Parallel ForEach](https://chrisstclair.co.uk/multithreading-made-easy-parallel-foreach/)

```js
var barcodedImages = new ConcurrentBag<barcodedimage>();
Parallel.ForEach(images, (image) =>
{
	//The below opeartion takes 4-5 seconds
	string barcode = ReadBarcode(image);
	barcodedImages.Add(new BarcodedImage(barcode,image));
});

//OR
object _lockObj = new object();
Parallel.ForEach(images, (image) =>
{
	//The below opeartion takes 4-5 seconds
	string barcode = ReadBarcode(image);
	lock(_lockObj)
	{
	  barcodedImages.Add(new BarcodedImage(barcode,image));
	}
});
```</barcodedimage>

origin - https://www.pipiscrew.com/?p=19348 parallel-foreach