---
layout: post
title: "Perl Weekly Challenge W029 - Brace Expansion"
tags: ["brace", "expansion", "perl"]
categories: ["pwc"]
comments: true
---

One of the task for this week's challenge is to write a script to demonstrate brace expansion. 
The script should take command line argument **```"Perl {Daily,Weekly,Monthly,Yearly} Challenge"```** and should expand it and print like below:
```
 Perl Daily Challenge
 Perl Weekly Challenge
 Perl Monthly Challenge
 Perl Yearly Challenge
```
<!-- more -->
### **Solution:**
I removed empty **```braces {}```** from the input string then I used a recursive function to check if the string has valid braces to expand. The use of **```[^{}]*```** instead of a simple **```.*```** to match the contents of the braces ensures that the inner most brace is processed first. 

I used perl's special variables **`` $` ``** and **```$'```** (same as **```$PREMATCH```** and **```$POSTMATCH```**) and stored them in **```$l```** and **```$r```** variables. Then captured the contents of the matching braces with **```()```** this was stored in **```$1```** and also assigned to the **```$m```** variable. The captured content was split using comma **```(,)```** as delimiter then each element were prepended and appended with the **```$l```** and **```$r```** variables respectively.

### **Code:**
```perl
use strict;
use warnings;
use feature 'say';

die "Usage:\n\tch-1.pl <string>\n\n" if @ARGV < 1;
die "Requires exactly one(1) string\n" if @ARGV > 1;
#Note(s):
#   - The script needs exactly one string enclosed with ""
#   - Unmatched braces will not be expanded
#   - The words will be printed in order that they appear inside the {}

#Remove empty braces before processing using substitution
#    The /r returns the result without modifying the $ARGV[0] variable
#Then the resulting string will be used in recusrive function expand
expand($ARGV[0]=~s/{}//gr);

sub expand {
    #The string will be stored in $string
    my $string = shift;

    # Regex was used to check if the string contains matching braces
    # Notice that [^{}]* instead of a simple .* to match the 
    # contents of the braces this is to make sure that the inner 
    # most brace is processed first

    # The matching string was captured using () and will be stored in $1
    if ($string =~ /{([^{}]*)}/) {
        # The captured value, the prematch and postmatch were stored
        # in variables $l,$m and $r respectively
        my ($l,$m,$r) = ($`,$1,$');

        # The captured value stored in $m was split 
        # using comma(,) as delimiter
        # The resulting list was used in a for loop
        for (",$m"=~/,([^,]*)/g) {
            # A new string containing the prematch
            # a value from the split operation of $m
            # and the postmatch will be used in the
            #  recursive function

            #The process will be repeated until...
            expand($l.$_.$r);
        }
    } else {
        #The string does not have matching braces.
        #Then final string will be printed
        say $string;
    }
}
```

### **Output:**
```perl
perl ch-1.pl "Perl {Daily,Weekly,Monthly,Yearly} Challenge"
Perl Daily Challenge
Perl Weekly Challenge
Perl Monthly Challenge
Perl Yearly Challenge

perl ch-1.pl "{The,A} quick {brown,gray} fox jumps over the lazy {dog,cow}"
The quick brown fox jumps over the lazy dog
The quick brown fox jumps over the lazy cow
The quick gray fox jumps over the lazy dog
The quick gray fox jumps over the lazy cow
A quick brown fox jumps over the lazy dog
A quick brown fox jumps over the lazy cow
A quick gray fox jumps over the lazy dog
A quick gray fox jumps over the lazy cow
```
