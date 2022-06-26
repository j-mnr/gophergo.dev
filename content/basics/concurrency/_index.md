---
Lesson: 23
Title: Concurrency
---

# Let's Get _Go_-ing with Concurrency!

Go has very, _very_, good support for concurrency. No matter what programming
language you are coming from, this is a good time 🕐 to sit down and read 👓
because you're going to learn something new ... Unless you're coming from {{<
anchor-new-window "Erlang"
"https://en.wikipedia.org/wiki/Erlang_(programming_language)" >}} in which case
you've got concurrency down 👍 and could teach me something about it 😂 Though
the code examples will still be useful to see how it's done in Go.

So first things first, {{< anchor-new-window "Concurrency is not parallelism"
"https://www.youtube.com/watch?v=oV9rvDllKEg" >}} this is an absolutely
necessary talk for any budding gopher by Rob Pike. We'll find out with the
coming lessons that concurrency is unlocked 🔓 with the power of goroutines,
channels, condition variables and {{< anchor-new-window
"monitors"
"https://en.wikipedia.org/wiki/Monitor_(synchronization)#Condition_variables"
>}} 🕵

Second, what's concurrency? The ability to do tasks without blocking execution
of other code. This is an incomplete definition 😓 but we need to start
somewhere! Let's say you have 3 tasks you need to complete: 1. Make dinner 🍽️
2. Do Laundry 🧺 3. Watch the latest episode of your favorite show 📺 How do
you do all three? 🤔 Sit in front of a pot of water, wait until it boils,
put in some potatoes 🥔 and wait for them to get soft? NO! 🙅 I don't know
about you, but for me it would look like this:

- **Task 1** -- Put the pot on the stove with water to boil 🧑‍🍳
- **Task 2** -- Collect all clothes 👚 👕 🎽 🩳 👖 into a basket 🧺
- **Task 1** -- Pull out ingredients for dinner 🥦 🍆 🥚 🧈 🧀 🧅 🥬
- **Task 3** -- Turn TV 📺 on and pull up latest episode
- **Task 2** -- Put clothes in washer 🧺 🧼 👔 🚿
- **Task 1** -- Watch show until water 💧💨 boils
- **Task 1** -- Add ingredients to stew 🍲
- **Task 3** -- Watch show 🛋️ until washer is done
- **Task 2** -- Move clothes from washer to dryer 🌬️👔
- **Task 3** -- Watch show 🛋️ until dinner is ready
- **Task 1** -- Setup dinner 🍽️
- **Task 2** -- Pull clothes 🧥 out of ♨️ dryer ♨️
- **Task 3** -- Finish 🛋️ watching show

See how all three tasks got done, but none of them were ever blocking the other
tasks? It was only **me** that was switching from each task that made it seem
like all three got done at the same time (in parallel), but what actually
happened is when I was waiting for something to finish, I didn't just stand
there 🧍 I picked up another task.

**I did not do all my tasks at the same time, I did them when they were ready
to move to their next steps** -- this is concurrency. If I did each one of them
at the same time (Let's say I had two friends come over) then it would be
parallelism. We can see that in this case 💼 Having a friend watch water boil
and another friend watch the washer until it turns off **wouldn't make the
process any quicker**

We can see when I was alone dealing with all my tasks, I got them done in the
same time it would take for three 🧑🧔👩 of us to do each task separately.
That's the difference with concurrency and parallelism; _dealing_ (concurrency)
with many things versus _doing_ (parallelism) many things. Concurrency is not
parallelism. It _allows_ for parallelism.

And as a final note, many of the lessons will be going into heavy detail around
concurrency. There are still plenty of coding examples, but it's important to
recognize the ideas and patterns of concurrency, before you jump in head first
🏊 Concurrency is a **_notoriously_** difficult topic to handle.

Just because Go makes it _incredibly easy_ to use concurrency, doesn't mean Go
will make concurrency any easier. Meaning, it's easy to blame your faults on
the tool, instead of admitting your depth of knowledge and lack of
understanding is the reason the application is failing. After all, you wouldn't
blame a chainsaw ⛓️🪚 for cutting the table in half when you tried to cut the
butter 🧈 would you? 😏 You have full knowledge 🧠 of when and where to use
a chainsaw. Now let's _Go_ 😹 make that a reality with concurrency! 💪😁
