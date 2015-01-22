1. Coming Across Problems
   A. Finding an accessible and PE-based way to explore current
architecture/library choices
       > A PE-based front-end framework would fit here
   B. Things we run into regularly that we learn about along the way
       > Rendering the page has to wait on a gigantic clump of CSS,
and async loading css means tons of FOUC, so if we could just get the
applicable css in the page inline and then async load the rest =
CriticalCSS
       > Sprite sheets are a big pain to deal with and icon fonts
don't work everywhere and are rough to use with fallbacks, we need a
retina-friendly and easy to use solution with automatic fallbacks =
Grunticon

2. Common Patterns
   A. Something we handle during the build process
       > Fit in as a Grunt task
       > If it would work as a bookmarklet or add-on, run in
phantomjs to generate
   B. Something that's reusable module
       > Check requirements - Does it require jQuery/Shoestring/some Dom
       > Lib?
       > Does it require maintaining a state?

3. Design Patterns
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

4. Shipping - How OSS Works
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