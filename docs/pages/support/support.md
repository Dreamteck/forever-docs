# Support

## 6.1 Contact Support

For any issues, questions and feature requests, you can contact Dreamteck Support at
support@dreamteck.io or write to support using our web form -
https://dreamteck.io/team/contact.php Please, make sure to select “Support” from the
dropdown menu in the form. Otherwise our team might take long to respond.

Or join the Dreamteck Discord server to chat with other developers who use our plugins.
Upon joining, make sure to mention @Dreamers and let us know that you are a developer.
You will be assigned the developer role which will give you access to the Forever channel
where you can ask questions about Forever. Please note that asking for Forever support in
other channels might result in the message getting deleted.

Join our Discord server: https://discord.gg/bkYDq8v

## 6.2 Frequently Asked Questions

Q: Is Forever suitable for mobile games?

A: Yes, Forever is optimized and tested on cell phones from the years 2014- 2019 – no
visible spikes in performance have been noticed. However, it is still possible to have some
spikes on some budget tier devices.

_________________

Q: Does Forever work on Unity 5.6 or older?

A: We haven’t tested it on 5.6 and have no reason to think it does.

_________________

Q: Does Forever build for all platforms?

A: Yes, Forever isn’t using platform-specific code. If a building error occurs, please contact
support so we can resolve the issue.

_________________

Q: Do I need programming experience to use Forever?

A: For the most part, no. The basic Forever features like level generation and running work
out of the box. However, some basic programming will be required to bind the player
input to the runner components (see Using Runners in Code for examples).

_________________

Q: Can I create levels with junctions with Forever?

A: Forever only generates one path but it is possible to do so:

- Create a junction opening segment where the terrain branches
- Create random segments that are branched and fit the opening segment
- Create a closing segment which merges the branched segments
- Add the segments to a nested sequence

_________________

Q: Can I create a game entirely out of custom segments and not use extrusion?

A: Yes, this is absolutely possible but the level generator will still require a path generator
to be assigned. A suitable solution for this is to just create a simple “High Level Path
Generator” and assign it. If all segments are set to custom, a path will not be generated at
all.