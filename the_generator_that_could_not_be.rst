===============================
The Generator that Could Not Be
===============================


Your friend `George <https://en.wikipedia.org/wiki/Georg_Cantor>`_ is a real math whiz. He has some very interesting ideas about infinity. Every now and again h, but they never quite seem to stick. Perhaps its the `formal terminology <https://www.transfinite.com/content/about5>`_ he uses. Perhaps it's the `unfamiliar notation <http://mathworld.wolfram.com/Aleph-0.html>`_. Maybe it's both! Regardless, you resolve to find a common ground. 

You may not be a strong theoretical mathematician, but you *do* happen to know a bit of Python. George thinks that ought to be enough to get started on the path toward taming the infinite. Heartened by his confidence, you decide to sit down with him and let him try to guide you down this arcane path.

------------

George suggests your first step ought to be to try to find a representation on the `natural numbers <http://mathworld.wolfram.com/NaturalNumber.html>`_. Of course, computer memory isn't infinite, so you can't actually create a infinite sequence of numbers. Lists, tuples, and arrays are all out. 

Having never been one to be put off by the apparent lack of an obvious built-in for your problem, you start to play around in a REPL session.

.. code-block:: python

    n = -1

    def subsequent_natural():
        global n
        n += 1
        return n

    subsequent_natural() # 0
    subsequent_natural() # 1 
    subsequent_natural() # 2


While functionally correct, that :code:`global` simply must go. Maybe try it as a class.

.. code-block:: python

    class NaturalsProducer(object):
        def __init__(self):
            self.n = -1

        def produce(self):
            self.n += 1
            return n

    naturals = Naturals()

    naturals.produce() # 0
    naturals.produce() # 1
    naturals.produce() # 2


You scrutinize the implementations side-by-side. Isn't there a more plain way of saying "subsequent" in :code:`subsequent_natural`? And what about "Producer" in the class name? It hits you like a bolt from the blue: 

"subsequent" -> `next <https://docs.python.org/3/library/functions.html#next>`_!

"Producer" -> `generator <https://www.python.org/dev/peps/pep-0255/>`_!

.. code-block:: python

    def naturals():
        n = 0
        while True:
            yield n
            n += 1

    N = naturals()
    
    next(N) # 0
    next(N) # 1
    next(N) # 2
    # and so on ...

    
In fact, there's an even more succinct expression possible. You combine the powers of :code:`itertools` and `generator expressions <https://www.python.org/dev/peps/pep-0289/>`_:

.. code-block:: python

    import itertools

    def naturals():
        return (n for n in itertools.count())


You can use this same idea to express the odds, evens, etc.

.. code-block:: python

    def odds():
        n = 1
        while True:
            yield n
            n += 2

    Odds = odds()

This seems all fine and good as far as it goes. You start work on a Primes generator. George waits patiently for you to run a few tests. Once you are satisfied he inquires: 

"How many primes are there again?" 

You recall `Euclid's demonstration <https://mathcs.clarku.edu/~djoyce/java/elements/bookIX/propIX20.html>`_ that there are infinite primes.

"Infinitely many." 

Your terseness belies your anticipation of his next question. You know that something fun must be around the corner.

"How about, oh, numbers that end in 0?"

You smile to yourself as you dash off the following into the buffer:

.. code-block:: python

    def ends_in_zero():
        n = 0
        while True:
            yield n
            n += 10
            yield -n

    Ends_in_Zero = ends_in_zero()


You cackle maniacally as you prepare to call :code:`len([_ for _ in Ends_in_Zero])` knowing full well this will pin a core and render the current terminal sesssion useless. 

George can sense when he's pushed too hard. He agrees to be more direct.

"Seriously, though. It's the same thing. They're both infinite."

What about the second :code:`yield` in :code:`ends_in_zero()`? Doesn't that somehow produce twice as many numbers as, say, :code:`naturals()` which will only :code:`yield` once per iteration?

"You are right, of course! There *are* just as many numbers ending in 0 as there are naturals, even when including the negative ones."

"The key is that we can map every natural number to precisely one number being produced in the new generator."

Here is a different way to express :code:`ends_in_zero()`:

.. code-block:: python

    def map_nat_to_eiz(n):
        if n == 0:
            return 0
        elif n % 2 == 0:
            return (n // 2 * -1) * 10
        else:
            return ((n + 1) // 2) * 10
    
    def ends_in_zero():
        N = naturals()
        return (map_nat_to_eiz(_) for _ in N)
    
    Ends_in_Zero = ends_in_zero()


This version helps to make the one-to-one correspondence more obvious explicit. 

Every time :code:`next(Ends_in_Zero)` is called, :code:`N` is advanced to produce a new value. 

We could use this as an informal definition of one-to-one correspondence:

	As long as the series you want to express can be generated by 
	calling a mapping function (that returns precisely one value) 
	for every value yielded by N, then that series must be of the 
	same "size" of N.

Mathematicians call this the *cardinality* of a set.

George shows some clever mappings.

.. code-block:: python

    # Map naturals to rationals to show 
    # they have one-to-one correspondence
    def inverse_paring(n):
        pass


"Neat! So I just need to write a function and I can show *any* sequence is the same cardinality of the naturals."

You start packing your things up, glad that you were finally able to pick up on George's ideas.

"Well..." you hear George start in.

"What do you think about the Reals? Say, all the reals between [0,1]."

You immediately start to grow unsure. Why did he always do this? 

Aren't some (maybe a lot) of the reals represented by infinite sequences?

.. code-block:: python

    # Consider the zero and decimal point implicit
    def one_third():
        while True:
            yield 3


    One_Third = one_third() # Never-ending stream of 3s


Seems fine so far. And there's no issue with a generator that yields other generators, right?

.. code-block:: python
    
    # 0.111111111111111111...
    def point_1_repeating():
        while True:
            yield 1


    # 0.12121212121212121212...
    def alternating_sequence():
        while True:
            yield 1
            yield 2
   

    def some_reals():
        yield point_1_repeating()
        yield 0.1 
        yield alternating_sequence()
        yield 0.2
        yield one_third()
        # etc. 


So far it is not clear what George is hinting at. True, it isn't obvious how to write the mapping function from the naturals to the reals. Nor was it obvious how to map to the rationals!

You need to be going, but agree to meet with George next week for what he promises will be a thrilling conclusion.

A week has passed. 

George asks if you were able to write the mapping function from the naturals to the reals.

"Sadly it has escaped me." you sheepishly admit. "I look forward to seeing your clever implementation, though!"

"Oh, don't feel bad! I actually want you to show you something simple. I want to show you that it can't be done."

.. code-block:: python

    def mirror_digit(n):
        plus_two = n + 2
        if plus_two < 10:
            return plus_two
        return plus_two % 2


    def brand_new_real(R):
        digit_place = 1
        for real in R:
            for i in range(0, digit_place): # Advance
                nth_digit = next(real)
            yield mirror_digit(nth_digit)
            digit_place += 1


"Wh-what *is* this?" 

:code:`mirror_digit` takes a digit [0-9] and returns the provided digit plus 2. If the given digit + 2 would result in a two-digit number, it just wraps back around to 0. This function allows us to create a sequence of numbers we haven't seen yet. 

+-----+-----+
|Input|Ouput|
+-----+-----+
|0    |2    |
+-----+-----+
|1    |3    |
+-----+-----+
|2    |4    |
+-----+-----+
|3    |5    |
+-----+-----+
|4    |6    |
+-----+-----+
|5    |7    |
+-----+-----+
|6    |8    |
+-----+-----+
|7    |9    |
+-----+-----+
|8    |0    |
+-----+-----+
|9    |1    |
+-----+-----+

For example, if you composed :code:`mirror_digit` with one of the reals generators (such as :code:`alternating_sequence()`), you would get a new number that would differ from the original number by **every single digit**.

We can exploit this to generate a previously ungenerated real.

We create a sequence where we generate:

* the mirror of the first digit of the first real
* the mirror of the second digit of the second real
* the mirror of the third digit of the third real
* and so on ...

(That's what's going on with that :code:`digit_place` variable. I want to call :code:`next()` as many times as reals generators I've seen so far.)

Here's the crux of it! When George said it can't be done, it's because you can always generate a new real using the power of :code:`brand_new_real()`.

Here's how the argument goes:

* Assume we *can* write a generator to produce all the reals :code:`Reals()`
* (Which, of course, would mean there'd be a one-to-one correspondence with the naturals)
* However, we've *also* shown that we can generate a new real with :code:`brand_new_real()`
* This is a problem! :code:`Reals()` will never be able to produce :code:`brand_new_real()`
* :code:`Reals()` must be incomplete. More accurately, :code:`Reals()` simply cannot do what it claims. 
* It *isn't possible* to map the naturals to the reals.

There are somehow infinitely "more" reals than naturals. In more formal terms, the set of the reals is said to have a greater cardinality than the set of the naturals. 

