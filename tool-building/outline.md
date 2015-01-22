Rough Thesis:

The ability to use, write, and publish open source build tools has
changed the way we work with the  front end - and the problems that we
face once we've decided to write something of our own are already
largely explored. Knowing what has worked in the past for other
platforms and how to maintain sanity in the face of creating tools in
the public will help you tremendously.

(This kind of tool building isn't new. People have been piping together
small bash scripts in make files for decades now. We don't need to
reinvent the wheel, but we end up doing it a lot anyway, only to find
out that their design decisions back then, make sense for our tooling
now)


1. Start - Coming Across Problems
   * Finding an accessible and PE-based way to explore current
architecture/library choices
      * A PE-based front-end framework would fit here
   * Things we run into regularly that we learn about along the way
      * Rendering the page has to wait on a gigantic clump of CSS,
and async loading css means tons of FOUC, so if we could just get the
applicable css in the page inline and then async load the rest =
CriticalCSS
      * Sprite sheets are a big pain to deal with and icon fonts
don't work everywhere and are rough to use with fallbacks, we need a
retina-friendly and easy to use solution with automatic fallbacks =
Grunticon

2. Common Patterns - Is this something we've solved before? Have we
   solved part of it? What type of tool do we need?
   A. Something we handle during the build process
       > Fit in as a Grunt task
       > If it would work as a bookmarklet or add-on, run in
phantomjs to generate
   B. Something that's reusable module
       > Check requirements - Does it require jQuery/Shoestring/some Dom
       > Lib?
       > Does it require maintaining a state?

3. The meat - Design Patterns
   A. Small libraries, do one thing well - follow the "unix philosophy"
       > Grunticon was originally a beast - now made up of 4
different parts, each individually tested and versioned
       > Arguments about small modules vs. frameworks don't really
apply here, these are tools, not large expected pieces. Specialize by
design.
   B. Work from the command line, take stdin, output to stdout
       > This is a thing that we didn't do with grunticon and it
bites me regularly - currently working on a switch
       > Grunticon still only works in the context of Grunt, this
isn't true with criticalcss which is a node library in itself, and can
then has another library that is a small grunt plugin wrapper for it
   C. Think deeply about your API, make sure it has a couple required
parameters, the rest should have defaults and, if in a lib, the
optional arguments should be passed in an options hash. If it is
async, make sure to include a callback function (that also has a
default) as a possible parameter.

4. What do you do when it's done? Shipping - How OSS Works
   A. Once you have cemented an API, use semver
       > Big change? Screw it, 2.0 now.
   B. There is weight in shipping, whether we like it or not
       > If people take interest, engage them
           - Give keys to those who contribute well, with small PRs
that fix single issues - they care
       > Put together contributing guidelines
           - Sometimes people are jerks, you want to have rules to point
             to.
       > If you are the owner of a famous project that has over 30
PRs, and you're not doing anything about them, close them. Inform
people. They put work into fixing things/adding functionality - don't
spurn them.
   C. Be ready to hand over the keys
       > It's stressful maintaining something popular, if somebody
else loves your project and wants to maintain it, let them.
   D. There is so much love in the community, embrace it.
