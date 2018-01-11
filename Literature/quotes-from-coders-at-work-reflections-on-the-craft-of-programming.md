# Quotes from _Coders at work. Reflections on the craft of programming_

I've just finished reading the book _Coders at Work. Reflections on the Craft of Programming_ by Peter Seibel (Apress, 2009). Even when it's not a technical book, there are a lot of interesting ideas to be learnt from it. It basically consists of a collection of interviews with 15 of the most interesting computer programmers ever, including [Donald Knuth][1], [Peter Norvig][2] or [Ken Thompson][3].

More information: [http://www.apress.com/9781430219484][4]

[1]: http://en.wikipedia.org/wiki/Donald_Knuth
[2]: http://en.wikipedia.org/wiki/Peter_Norvig
[3]: http://en.wikipedia.org/wiki/Ken_Thompson
[4]: http://www.apress.com/9781430219484


## Jamie Zawinski

> The other big thing was we always shipped all platforms simultaneously; that was another thing they thought was just stupid. "Oh, 90 percent of people are using Windows, so we'll focus on the Windows side of things and then we'll port it later." Which is what many other failed companies have done. If you're trying to ship a cross-platform product, history really shows that's how you don't do it. If you want it to really be cross-platform, you have to do them simultaneously. The porting thing results in a crappy product on the second platform.

> So, if anything, that makes the part of the skill set that is being able to dive into someone else's code and figure out how to make use of it even more important. "I don't understand this, so I'm going to write my own" worked better in the past. Whether it was ever a good idea or not, you could do it. It's much harder to get away with that now.

## Brad Fitzpatrick

> Before that I remember writing something that every time I hit a key, it moved the head and I had wired backspace up to go backwards so as I typed it felt like a typewriter.
>
> This was one of my first programs —it was something like K equals grab the next char. Then I said if K equals "a", print "a"; if K is "b", print "b". I pretty much did every letter, number, and some punctuation. Then at one point I was like, "Wait, I could just say, 'Print the variable!'" and I replaced 40 lines of code with one. I was like, "Holy shit, that was awesome!" That was some major abstraction for a six-year-old.

## Douglas Crockford

> The place where I found that to be most effective was taking testing, sort of, to the ultimate: going to visit customers. I did some of that early in my career and that was a great experience, having to go live with a customer for a week, helping them to install a new system, and helping them to work out the problems with using it.
>
> It gave me a huge amount of insight into what it's like to actually use our stuff and what I want to be doing for the benefit of the people who are going to be using my stuff. Going back afterwards, developers who had not had that experience all seemed arrogant to me in a way which was completely inexcusable. The lack of respect they had for the people who used our stuff was appalling and it was basically a consequence of their having never met those people.

## Joshua Bloch

> But the fundamental rule is, write the code that uses the API before you write the code that implements it. Because otherwise you may be wasting your time writing implementation code that won't get used. In fact, write the code that uses the API before you even flesh out the spec, because otherwise you may be wasting your time writing detailed specs for something that's fundamentally broken. That's how I go about designing stuff.

> It doesn't matter how good you are; you can't get an API right until you've tried to code to it. You design something; try to use it; and say, "Oh, this is so wrong." And if you do this before you've wasted time writing all of the layers underneath it, that's a huge win. So what I'm talking about is test-first programming and refactoring the APIs, rather than refactoring the implementation code underneath the APIs.

> **Seibel:** Since you mentioned Fowler, who's written a couple of books on UML, do you ever use UML as a design tool?
>
> **Bloch:** No. I think it's nice to be able to make diagrams that other people can understand. But honestly I can't even remember which components are supposed to be round or square.

> And if you do add complexity to it, will the language simply disappear? No, it won't. I think C++ was pushed well beyond its complexity threshold and yet there are a lot of people programming it. But what you do is you force people to subset it. So almost every shop that I know of that uses C++ says, "Yes, we're using C++ but we're not doing multiple-implementation inheritance and we're not using operator overloading." There are just a bunch of features that you're not going to use because the complexity of the resulting code is too high. And I don't think it's good when you have to start doing that. You lose this programmer portability where everyone can read everyone else's code, which I think is such a good thing.

## Joe Armstrong

> Also, I think today we're kind of overburdened by choice. I mean, I just had Fortran. I don't think we even had shell scripts. We just had batch files so you could run things, a compiler, and Fortran. And assembler possibly, if you really needed it. So there wasn't this agony of choice. Being a young programmer today must be awful —you can choose 20 different programming languages, dozens of framework and operating systems and you're paralyzed by choice. There was no paralysis of choice then. You just start doing it because the decision as to which language and things is just made —there's no thinking about what you should do, you just go and do it.

> I think the lack of reusability comes in object-oriented languages, not in functional languages. Because the problem with object-oriented languages is they've got all this implicit environment that they carry around with them. You wanted a banana but what you got was a gorilla holding the banana and the entire jungle.
>
> If you have referentially transparent code, if you have pure functions —all the data comes in its input arguments and everything goes out and leaves no state behind— it's incredibly reusable. You can just reuse it here, there, and everywhere. When you want to use it in a different project, you just cut and paste this code into your new project.

> I like documentation. I don't think a program is finished until you've written some reasonable documentation. And I quite like a specification. I think it's unprofessional these people who say, "What does it do? Read the code." The code shows me what it does. It doesn't show me what it's supposed to do. I think the code is the answer to a problem. If you don't have the spec or you don't have any documentation, you have to guess what the problem is from the answer. You might guess wrong. I want to be told what the problem is.

## Simon Peyton Jones

> Steven Clarke and his colleagues at Redmond have made systematic attempts to watch programmers, given a new API, talk through what they're trying to do. And they get the people who designed the API to sit behind a glass screen and watch them.
>
> And the guys sitting there behind the glass screen say, "No, no, don't do that! That's not the right way!" But it's soundproof. That turns out often to be very instructive. They go and change their API.

> I think my default is not to write something very general to begin with. So I try to make my programs as beautiful as I can but not necessarily as general as I can. There's a difference. I try to write code that will do the task at hand in a way that's as clear and perspicuous as I can make it. Only when I've found myself writing essentially the same code more than once, then I'll think, "Oh, let's just do it once, passing some extra arguments to parameterize it over the bits that are different between the two."

> The most depressing thing about life as a programmer, I think, is if you're faced with a chunk of code that either someone else wrote or, worse still, you wrote yourself but you no longer dare to modify. That's depressing.

## Peter Norvig

> It's also important to know what you're doing. When I wrote my Sudoku solver, some bloggers commented on that. They said, "Look at the contrast —here's Norvig's Sudoku thing and then there's this other guy", whose name I've forgotten, one of these test-driven design gurus. He starts off and he says, "Well, I'm going to do Sudoku and I'm going to have this class and first thing I'm going to do is write a bunch of tests." But then he never got anywhere. He had five different blog posts and in each one he wrote a little bit more and wrote lots of tests but he never got anything working because he didn't know how to solve the problem.
>
> I actually knew —from AI— that, well, there's this field of constraint propagation —I know how that works. There's this field of recursive search —I know how that works. And I could see, right from the start, you put these two together, and you could solve this Sudoku thing. He didn't know that so he was sort of blundering in the dark even though all his code "worked" because he had all these test cases.
>
> Then bloggers were arguing back and forth about what this means. I don't think it means much of anything —I think test-driven design is great. I do that a lot more than I used to do. But you can test all you want and if you don't know how to approach the problem, you're not going to get a solution.

> I see tests more as a way of correcting errors rather than as a way of design. This extreme approach of saying, "Well, the first thing you do is write a test that says I get the right answer at the end," and then you run it and see that it fails, and then you say, "What do I need next?" —that doesn't seem like the right way to design something to me.
>
> It seems like only if it was so simple that the solution was preordained would that make sense. I think you have to think about it first. You have to say, "What are the pieces? How can I write tests for pieces until I know what some of them are?" And then, once you've done that, then it is good discipline to have tests for each of those pieces and to understand well how they interact with each other and the boundary cases and so on. Those should all have tests. But I don't think you drive the whole design by saying, "This test has failed."

## Guy Steele

> His contention was that if you took care of the edge cases then the stuff in the middle usually took care of itself. Well, he didn't say it that way; I guess that's the conclusion I draw from him.
>
> To turn it around, you want to design the specification of what's in the middle in such a way that it naturally is also correct on the boundaries, rather than treating boundaries as special cases.

> **Seibel:** How much does a choice of language really matter? Are there good reasons to choose one language over another or does it all just come down to taste?
>
> **Steele:** Why shouldn't taste be a good reason?

> **Seibel:** Do you think languages are getting better? You keep designing them, so hopefully you think it's a worthwhile pursuit. Is it easier to write software now because of advances that we've made?
>
> **Steele:** Well, it's much easier now to write the kinds of programs we were trying to write 30 years ago. But I think our ambitions have grown tremendously. So I think programming is probably a more difficult activity than it was 30 years ago.

> If I could change one thing —this is going to sound stupid— but if I could go back in time and change one thing, I might try to interest some early preliterate people in not using their thumbs when they count. It could have been the standard, and it would have made a whole lot of things easier in the modern era. On the other hand, we have learned a lot from the struggle with the incompatibility of base-ten with powers of two.

## Dan Ingalls

> There's this wonderful little hack that you can do in various places in JavaScript. It's a little bit expensive, but JavaScript has this curly bracket object notation, and so you can use keywords and they actually looks like Smalltalk keywords because they end with colons, so you can have your multiple arguments be in a curly brace expression. It actually makes nice-looking programs.

## Peter Deutsch

> The difference is that the principles for dealing with algorithmic problems are based a lot more directly on 5,000 or 10,000 years' worth of history in mathematics. How we go about programming now, we don't have anything like that foundation to build on. Which is one of the reasons why so much software is crap: we don't really know what we're doing yet.

> You know the old story about the telephone and the telephone operators? The story is, sometime fairly early in the adoption of the telephone, when it was clear that use of the telephone was just expanding at an incredible rate, more and more people were having to be hired to work as operators because we didn't have dial telephones. Someone extrapolated the growth rate and said, "My God. By 20 or 30 years from now, every single person will have to be a telephone operator." Well, that's what happened. I think something like that may be happening in some big areas of programming, as well.

> There has to something a little wrong with you for you to be a really good programmer. Maybe "wrong with you" is a little too strong, but the qualities that make somebody a well-functioning human being and the qualities that make somebody a really good programmer —they overlap but they don't overlap a whole heck of a lot. And I'm speaking as someone who was a very good programmer.

> So responding to your question about whether the way I programmed in the small changed as I started to work with larger systems, the answer is, yes. As I built larger and larger systems, I found that when sitting down to write any piece of code, more and more the question I would ask myself first is, "OK, what's the interface between this and everything around it going to look like? What gets passed in? What gets passed out? How much of a task should be on which side of an interface?" Those kinds of questions started to become a larger and larger part of what I was dealing with. And they did affect the way that I wrote individual smaller chunks of code, I think.

## Ken Thompson

> Modern programming scares me in many respects, where they will just build layer after layer after layer that does nothing except translate. It confuses me to read a program which you must read top-down. It says "do something." And you go find "something." And you read it and it says, "do something else" and you go find something and it says, "do something else" and it goes back to the top maybe. And nothing gets done. It's just relegating the problem to a deeper and deeper level. I can't keep it in my mind —I can't understand it.

> I'll do it as simply as possible the first time and very often that suffices for all time. To build a very complex algorithm for something that's never run is just stupid. It's just a waste of time. It's a bug generator. And it makes it impossible to maintain because you've got to have 50 pages of math to tell the next guy what you're actually doing.
>
> Ninety-nine percent of the time something simple and brute-force will work fine. If you really are building a tool that is used a lot and it has some sort of minor quadratic behavior sometimes you have to go in and beat on it. But typically not. The simpler the better.

## Bernie Cosell

> I brought up Knuth as an example. I would not teach students Knuth per se for two reasons. First, it's got all this mathematical stuff where he's not just trying to present the algorithms but to derive whether they're good or bad. I'm not sure you need that. I understand a little bit of it and I'm not sure I need any of it. But getting a feel for what's fast and what's slow and when, that's an important thing to do even if you don't know how much faster or how much slower.
>
> The second problem is once students get sensitive to that, they get too clever by half. They start optimizing little parts of the program because, "This is the ideal place to do an AB unbalanced 2-3 double reverse backward pointer cube thing and I always wanted to write one of those." So they spend a week or two tuning an obscure part of a program that doesn't need anything, which is now more complicated and didn't make the program any better. So they need a tempered understanding that there are all these algorithms, how they work, and how to apply them. It's really more of a case of how to pick the right one for the job you're trying to do as opposed to knowing that this one is an order n-cubed plus three and this one is just order n-squared times four.

> When you have to fix a bug in a program you never, ever fix the bug in the place where you find it. My rule is, "If you knew then what you know now about the fact that this piece of code is broken, how would you have organized this piece of the routine?" What were you thinking about wrong before? Fix the code so that can't happen. When you finish with a routine I want every routine you work on to look as if it was just written. I do not want to see any evidence of afterthoughts or things gone wrong followed by something to correct the error or a mysterious piece of code saying, "This routine returns the wrong value every now and then so I've got to fix it." I don't want to see any of that. I want to see code that looks like through some divine inspiration you got it exactly right the first time.

> Programmers are the worst optimizers in the world. They always optimize the part of the code that's most interesting to optimize, and almost never get the part of the code that actually needs optimization. So you get these little nuts of very difficult code that have no point. I always tell the people working with me, "Code it as lucidly, as easy to read, as crystal-clear as you can. Do it the simple way. And then if it needs to be sped up, we'll deal with that later. If you've done it right, we can draw a little box around this piece."

> At one level I'm thinking, "This is way cool that you can do that." The other level, the programmer in me is saying, "Jesus, I'm glad that this wasn't around when I was a programmer." I could never have written all this code to do this stuff. How do these guys do that? There must be a generation of programmers way better than what I was when I was a programmer. I'm glad I can have a little bit of repute as having once been a good programmer without having to actually demonstrate it anymore, because I don't think I could.

## Donald Knuth

> The problem is that coding isn't fun if all you can do is call things out of a library, if you can't write the library yourself. If the job of coding is just to be finding the right combination of parameters, that does fairly obvious things, then who'd want to go into that as a career?
>
> There's this overemphasis on reusable software where you never get to open up the box and see what's inside the box. It's nice to have these black boxes but, almost always, if you can look inside the box you can improve it and make it work better once you know what's inside the box. Instead people make these closed wrappers around everything and present the closure to the programmers of the world, and the programmers of the world aren't allowed to diddle with that. All they're able to do is assemble the parts.

> To me one of the most important revolutions in programming languages was the use of pointers in the C language. When you have nontrivial data structures, you often need one part of the structure to point to another part, and people played around with different ways to put that into a higher-level language. Tony Hoare, for example, had a pretty nice clean system but the thing that the C language added —which at first I thought was a big mistake and then it turned out I loved it— was that when x is a pointer and then you say, x + 1, that doesn't mean one more byte after x but it means one more node after x, depending on what x points to: if it points to a big node, x + 1 jumps by a large amount; if x points to a small thing, x + 1 just moves a little. That, to me, is one of the most amazing improvements in notation.

> I always try things that are at my limit. If I had to go back and write those kinds of programs again, the easier ones, I wouldn't make so many mistakes. But now that I know some more, I'm trying to write harder stuff. So I make mistakes because I'm always operating at my limit. If I only stay in comfortable territory all the time, that's not so much fun.