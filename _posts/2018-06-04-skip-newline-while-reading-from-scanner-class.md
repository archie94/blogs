---
layout: post
cover: 'assets/images/java.jpg'
title: Skip newline character while reading from Scanner class
date:   2018-06-04 00:00:00
tags: development
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/home.png'
---

We have all come across reading from `stdin` while programming in Java and `java.util.Scanner` class provides easy way to parse expressions and give out primitive types. From the doc :

	A Scanner breaks its input into tokens using a delimiter pattern, which by default matches whitespace. The resulting tokens may then be converted into values of different types using the various next methods.


So let's look at the following code: 

	Scanner scanner = new Scanner(System.in);
	
	int n = scanner.nextInt();
	
	while(n-- > 0) {
	    String s = scanner.nextLine();
	    System.out.println(s);
	}

Clearly this piece of code spits out the words we type in. The first input should be a `Integer` denoting number of lines to take as user input. So for the input 

	3
	asdf qwerty
	Hello World
	Hello java

we have the output


	asdf qwerty
	Hello World

Why is that? Experienced folks are quick to catch that the `scanner.nextInt()` does not consume the newline character. Workaround? Easy forcibly consume the newline character after the `scanner.nextInt()` read. Something like 

    Scanner scanner = new Scanner(System.in);

    int n = scanner.nextInt();
    scanner.nextLine();

    while(n-- > 0) {
        String s = scanner.nextLine();
        System.out.println(s);
    }

And we get what we want. Another workaround: read the whole line and then parse it as an integer. Something like

	try {
	    n = Integer.parseInt(scanner.nextLine());
	} catch (NumberFormatException e) {
	    e.printStackTrace();
	}

Works! 

But recently I came across the `skip()` method available in the `Scanner` class. It works by taking in a pattern/regex and will skip the input that matches the said pattern. So lets do this 


    Scanner scanner = new Scanner(System.in);

    int n = scanner.nextInt();
    scanner.skip("(\r\n|[\n\r\u2028\u2029\u0085])?");

    while(n-- > 0) {
        String s = scanner.nextLine();
        System.out.println(s);
    }


Whoa! Works but what did I just do? Well we are just telling scanner to skip any newline character after the interger read. Well why the whole `"(\r\n|[\n\r\u2028\u2029\u0085])?"`? Interestingly line break control character is different for different OS! Linux has `\n` escape sequence, Mac has `\r` while windows keeps `\r\n`. The stated pattern asks the `Scanner` class to remove them. Additionally 


	\u2028 is Unicode character LINE SEPARATOR
	\u2029 is Unicode character PARAGRAPH SEPARATOR
	\u0085 is Unicode character NEXT LINE (NEL)


This piece of code looked elegant to me; we are skipping the newline knowing what we can except although agreed maybe the previous method are more memorable. A big thanks to [Hackerrank](https://www.hackerrank.com/dashboard); found this piece of code in their prefilled code for [String Construction](https://www.hackerrank.com/challenges/string-construction/problem) problem.

Cheers!