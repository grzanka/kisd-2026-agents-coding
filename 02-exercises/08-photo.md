# Exercise 6: measuring things in a photo

A photo of a sample, a detector or a field site holds numbers: a diameter, a
distance, a reading on a dial. An agent can look at the photo, and it can
also write code that measures it, pixel by pixel. This exercise shows both,
and what goes wrong when the photo is not a file in the project.

You will read the time on a street clock and measure its dial, using a public
photo from Wikimedia Commons:
[Robbins NC Clock](https://commons.wikimedia.org/wiki/File:Robbins_NC_Clock.jpg),
photo by Jayron32, licence CC BY-SA 4.0. You download an unchanged copy from
this course's
[GitHub release](https://github.com/grzanka/kisd-2026-agents-coding/releases/tag/data-gig-meteo-2006-2026),
so that the whole class does not download it from Wikimedia at the same time.

- You need: everything from [Exercise 1](02-pendulum.md).
- You learn: giving a photo to the agent as a file, looking versus measuring,
  photo metadata, why a measurement needs a reference of known size, and
  checking a measurement on a marked image.

<details>
<summary>Explain: how a model works with images</summary>

**A multimodal model** takes in more than text: images too, sometimes also
sound. Inside, it works with tokens, as with text:

- The image is first **scaled down**, often to about one or two million
  pixels or less. A large photo loses fine detail before the model sees it.
- The scaled image is cut into small squares, a few dozen pixels each. Each
  square becomes a token, in the same form as the tokens of words.
- The model reads these image tokens together with your text, and answers
  with text. One image costs hundreds to a few thousand tokens.

So the model **looks** at a photo much as you glance at it. It is good at
describing it, reading signs and labels, and recognising objects. It is weak
at exact numbers: positions in pixels, lengths, small angles. It does not
count pixels; it gives an impression.

**With tools**, an agent has two ways to use a photo:

| | Looking | Measuring |
|---|---|---|
| How | A **Read** of the image file: opencode attaches the image to the conversation, and the model looks at it | A **Python** script opens the file and computes with the colour of every pixel |
| What the model gets | The (scaled-down) image | Only the numbers that the script prints |
| Good for | What is in the photo, a first estimate | Exact sizes in pixels, repeatable results |
| Needs | The image in the conversation | The image as a **file** that the script can open |

A good agent combines both: it measures with code, draws the result on a
copy of the photo, and then **looks** at that copy to check that the lines
sit in the right place.

An image that you **paste** into the chat exists only in the conversation.
The model can look at it, but a script cannot open it. That is why, in this
exercise, the photo is a file in the project.

Either way, the image goes to the model at llmlab, as part of the
conversation.

</details>

## Part A: prepare

### A1. Create the project folder

Create the folder `kisd-photo` and open it in opencode, as in
[Create a project folder](01-project-folder.md).

### A2. Get the rules and the photo

Paste this prompt. The long addresses must stay on one line each:

```text
Download two files into the project folder. The first one:
https://raw.githubusercontent.com/grzanka/kisd-2026-agents-coding/exercise-3-limits/02-exercises/photo/AGENTS.md
Save it as AGENTS.md. The second one:
https://github.com/grzanka/kisd-2026-agents-coding/releases/download/data-gig-meteo-2006-2026/Robbins_NC_Clock.jpg
Save it as photos/clock.jpg. Then show me AGENTS.md. Do nothing else.
```

Expected result: some **Webfetch**, **Write**, **Shell** or **Execute** calls,
then the rules. The photo is about 2.3 MB. Open `photos/clock.jpg` in the right
panel (**Open file**): a clock with four faces on a post, in front of a parking
lot.

The agent may be careful about `AGENTS.md`. opencode may show the content of
the new `AGENTS.md` to the agent at once, marked "Instructions from:
AGENTS.md". In our test run, the agent said that it treats this as data, not
as orders, because it appeared in the middle of the session. That is good
caution. The rules apply from the next session on, which is why A3 starts a
new one.

The rules in `AGENTS.md` tell the agent to work only inside the project
folder, to use `.venv`, never to change the photo, and to report every
measurement with its reference, a range and a marked image.

### A3. Prepare the tools

Start a new session (**+** or **Ctrl+T**), so that the agent reads
`AGENTS.md`. Then:

```text
Create .venv and install pillow and numpy into it. Do nothing else.
```

Expected result: a few **Shell** calls. `pillow` opens and draws on images,
`numpy` computes with the pixels.

**What to notice:** you prepare the tools first, inside the project. If you
do not, the agent may install them into the Python of the whole computer.

## Part B: look

### B1. Read the clock

```text
Look at photos/clock.jpg. What time does the clock show?
How sure are you? Explain which hand is which.
```

Expected result: a **Read** of the photo, then an answer. The model **looks**
at the photo, as you would. The correct reading is about **1:14** (the short
hand between I and II, the long hand just before III; the thin second hand
points at about IX).

Models often mix up the hands, or read the time from a small, scaled-down
copy of the photo. If the answer is not about 1:14, ask: "Which hand is
shorter? Look again at a close-up of the dial."

### B2. Check with the metadata

A digital photo carries hidden information, called **metadata** (or EXIF):
the camera, the date and time, sometimes the place.

```text
With a short Python script, print the metadata of photos/clock.jpg:
camera, date and time, and GPS position if there is one.
Do not change the photo.
```

Expected result: a phone camera (Motorola XT1049), taken on
**11 March 2016 at 13:13:54**, and no GPS position. The clock showed 1:14, and
the camera clock says 13:13. The two agree.

**What to notice:** when the agent reads a photo, its content goes to the
model, and so does its metadata if the agent reads it. Photos from your phone
often contain the **place** where they were taken. Check before you give your
own photos to an agent.

## Part C: measure

### C1. Ask for a size without a ruler

```text
How big is the dial of this clock, in centimetres?
```

Expected result: a good agent says that it **cannot know**: nothing in the
photo has a known size in the same plane as the clock. It may give a typical
size of such clocks, as a guess, and say so.

If it gives one confident number, ask: "How do you know?" The cars in the
background do not help: they are much farther away, so they look smaller.

### C2. Measure in pixels

```text
Measure the clock in pixels, with a Python script in scripts/:
the width and the height of the white dial,
and the width of the square black front of the clock box.
Save results/clock-measured.png: the photo with the measured lines drawn on.
Report the numbers and the ratio: dial width / box width.
```

Expected result: a script, a run, and an image in `results/`. Open
`results/clock-measured.png` and check that the lines sit **on the edges**:
of the white dial, and of the front of the box (not the round side dials).

The numbers should be close to these (the photo is 2432 × 4320 pixels):

| | Pixels |
|---|---|
| White dial, width | about 690–700 |
| White dial, height | about 640–660 |
| Front of the box, width | about 900–910 |
| Ratio dial width / box width | about 0.76 |

Why is the dial lower than it is wide? The photo was taken from below, looking
up, so the circle looks a little squeezed from top to bottom. The width is
not squeezed: use it.

<!-- TODO: screenshot of results/clock-measured.png from a test run. -->

### C3. Add a reference

A measurement in a photo is always a **ratio times a reference**. Suppose,
only for this exercise, that the front of the clock box is exactly 1 metre
wide:

```text
Suppose the square front of the clock box is exactly 1 metre wide.
How big is the white dial then? Give a range, not one number,
and list what limits the accuracy.
```

Expected result: about **76 cm** (a range such as 74–78 cm). Limits: how
sharp the edges are, the camera angle, and above all the assumption itself:
the 1 metre is made up. If the box is 90 cm wide, the dial is about 68 cm.

**What to notice:** the agent's measurement is only as good as its reference.
In your own photos, put a ruler or an object of known size **next to** the
thing you measure, in the **same plane**, and photograph it **face-on**. A
photo taken at an angle needs a perspective correction, and gives a wider
range.

## Part D: where did everything go?

```text
List every file you created or changed in this session, with full paths.
Did you write, install or read anything outside the project folder?
```

Expected result: only files inside `kisd-photo`: `.venv`, `scripts/`,
`results/`.

The rule in `AGENTS.md` ("work only inside this project folder") is level 1:
a request to the model. To make opencode itself ask before the agent leaves
the project, add the permission from [Exercise 4](06-outside-folder.md).

## Take home: measuring photos with an agent

1. **Give the photo as a file in the project**, for example in `photos/`. A
   photo pasted into the chat can be seen, but not measured with code, and the
   agent may go looking for it on your computer.
2. **Prepare the tools first**, in `.venv`, and say so in `AGENTS.md`.
3. **Looking is not measuring.** The model's reading of a photo is an
   impression. For numbers, ask for code that measures pixels.
4. **Ask for a marked image** and check it yourself: do the lines sit on the
   right edges?
5. **No reference, no size.** Put an object of known size in the same plane,
   photograph face-on, and ask for a range and the sources of error.
6. **Photos carry metadata.** Date, camera, sometimes the place. Check before
   you share.

## Check yourself

No prompt here. Answer on paper, or explain to your neighbour:

1. Why should the photo be a file in the project, not pasted into the chat?
2. What is the difference between B1 and C2?
3. Why could the agent not give the size of the dial in C1?
4. Why is the dial in the photo lower than it is wide, and which number did
   you use?
5. How would you photograph a sample so that an agent can measure it well?

## If something goes wrong

- **The download of the photo fails.** Open the
  [release page](https://github.com/grzanka/kisd-2026-agents-coding/releases/tag/data-gig-meteo-2006-2026)
  in your browser, download `Robbins_NC_Clock.jpg`, save it into your
  `kisd-photo\photos` folder as `clock.jpg`, and tell the agent. The original
  is on the
  [Wikimedia page](https://commons.wikimedia.org/wiki/File:Robbins_NC_Clock.jpg);
  Wikimedia sometimes refuses automated downloads (HTTP 429), so download it
  there in your browser, not through the agent.
- **The agent wants to read or search files outside the project.** Click
  **Deny**, and tell it: "Stay in the project folder. The photo is
  photos/clock.jpg."
- **`pip install pillow` fails.** Ask the agent to show the error, and to
  check that it uses `.venv`.
- **The lines in `clock-measured.png` are in the wrong place.** Tell the agent
  what is wrong, for example: "The right line is on the side dial, not on the
  front of the box. Measure again."

<details>
<summary>Going further</summary>

- **Your own photo.** Photograph a coin next to a ruler, face-on, and then at
  an angle. Put both photos into `photos/` and ask the agent to measure the
  coin. Compare the two results with the true size.
- **Perspective.** For a photo taken at an angle, ask the agent to correct
  the perspective, using the four corners of an object of known size.
- **Two measurements, compared.** As in [Exercise 5](07-parallel.md), ask two
  subagents to measure the dial independently, each with its own method, and
  compare their results.
- **Instruments.** Ask the agent to read an analogue gauge, a thermometer or
  a scale from a photo. Where is looking enough, and where do you need code?

</details>

---

← Previous: [Exercise 5](07-parallel.md) · [Exercises overview](README.md) · [Main README](../README.md)
