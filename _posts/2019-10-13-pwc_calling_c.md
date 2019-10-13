---
layout: post
title: "Perl Weekly Challenge W029 - Calling C"
tags: ["c", "inline", "nativecall", "perl"]
categories: ["pwc"]
---
The second task for this week's challenge is to write a script to demostrate calling a C function in perl, it can be a user-defined or a standard(built-in) C function. 

I have seen implementation of this using the ```Inline``` module but I have to be honest that I have never really tried it in perl. Never had the need to use it or atleast that's what I thought. At the back of my mind, I knew a piece of code compiled in C would perform better compared to pure perl solution. 

So for this task, I created a ```fibonacci``` function that would take a number ```$n``` as input and would return the ```$n```-th number in the ```fibonacci``` series
<!-- more -->
### **Solution:**
### **Code:**
```perl
#!/usr/bin/env perl
# Write a script to demonstrate calling a C function. 
# It could be any user defined or standard C function.
use strict;
use warnings;
use feature 'say';
use Memoize;

use Inline 'C';

my $start_run;
my $run_time;


$start_run = time();
print (perl_fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Perl): $run_time sec\n";

memoize('perl_fib');
$start_run = time();
print (perl_fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Perl-Memoized): $run_time sec\n";

$start_run = time();
print (fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Inline-C): $run_time sec\n";

sub perl_fib { ($_[0] == 0) ? 0: ($_[0] == 1)?1:perl_fib($_[0]-1)+perl_fib($_[0]-2) }

__END__
__C__
unsigned long fib(int n) {
    if ( n == 0 )
        return 0;
    else if ( n == 1 )
        return 1;
    else
        return ( fib(n-1) + fib(n-2) );
}
```
### **Output:**

```
```

If you'd like to join the fun and contribute, please visit the [site](https://perlweeklychallenge.org/blog/) managed by [Mohammad S Anwar](https://github.com/manwar). 

