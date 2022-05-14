# IntroLab dependency analysis

## Class diagram

![IntroLab](http://www.plantuml.com/plantuml/svg/hLHHQnGn47xVNn7EbxMse8AWVP0Y5H4f5W_neQsGsqnlOxD9o6GzJl4N-HDywo_pDfSDsJZQ5KqVclcI-VBDTxDpWfuxttUgyiOoRzZAU6yw9gI3nakZAxoHsdB7EwQa1h-rm8pphQcgHd54zg9N2ZpxMZ7s80RrjJBS7xByt_rZ5cxqLd0FzOXzDb84N3Y-gPSjj_0Ej007RZpz5XzwgQ7RndV2lXuv4Vpfg1VhCkyLxo2c3TkKvG3b5wYbtacKUYB61Gbr3bdU8qmG8T6Q0ciS3DLDkR3lW5JrMjlUdplJ0A9nIMDva1JVmFQzhVTQLyOeu7h4Jyr65oVvIszWYoD_37FtTd7mOSpDzKiakBDVSdhtt04YY3z8MNhk8Mc9SPrsPrppDRXHdDJIrsMVJ82MQw7sZjtvyr3DbxGL2gB44gvdDN8Eu91zFBHOUkkCIntvXstYx8PWwPtKwukhKk2vujlSl1ZJvj4Mq7RDDBeikDG6BXCMZHhFbvOty7Rr2PeuAN8abRznt4wUDBr7AU23rC9iodQAhDXA6pbJd1Z9gPBeiJLcMZI23xVlAZpW2gvz-KbCOoVNhSzrLt5QJjRHnRs7k_NqyQ5LNEgheuziObXSrNu0pimDjgHLXI7uQ-wJPyFVeU3OWhWwUiwekNJC5hVVzvFjziU2ebeyMh23H1UbxDWMQ-mkzUwmxD2_bbGa5vBIZN-NL6A_vndl1hPI0Sr65vXzsMaL1OQ1CTEhn49J4O_HL6MxqLHpROU1BLX5y19CP6jJDiylA0wf_rIG3MoX8eAu8h3P7sl2PiXOXO62b_fWmShXGdFHxXJ9b3k9Qlxigav0YxvJFm40)

As we can see from the class diagram everything is "connected together".
This is because we are depending on concrete implementations instead of abstractions. And instead of injecting behaviour at runtime, each object knows the implementation details.

We can also see that `GameKeys` is not even being used and could just be deleted, the reason for that is that only enemy shooting randomly was implemented, and hence it should just be removed.

## Package dependencies

Let's go trough each of our 4 packages and see what dependencies they have.

#### Entities package

- `com.badlogic.gdx.graphics`
- `com.badlogic.gdx.graphics.glutils`
- `com.badlogic.gdx.math`
- `dk.sdu.mmmi.cbse.main`
- `java.lang`
- `java.util`

#### Gamestates package

- `com.badlogic.gdx.graphics.glutils`
- `dk.sdu.mmmi.cbse.entities`
- `dk.sdu.mmmi.cbse.managers`

#### Main package

- `com.badlogic.gdx`
- `com.badlogic.gdx.backends.lwjgl`
- `com.badlogic.gdx.graphics`
- `dk.sdu.mmmi.cbse.managers`
- `java.lang`

#### Managers package

- `com.badlogic.gdx`
- `dk.sdu.mmmi.cbse.gamestates`

### Things that stand out to me

#### Not encapuslating third party dependencies

Here I'm thinking of `com.badlogic.*`, this makes it harder to change to another framework in the future or if we want to split out our application into components, we would be prone to the flat class path problem, since we might end up with different versions of `com.badlogic`.

Instead we could utilise the adapter pattern to fx create our own version of a `ShapeRenderer` and create an interface for the created adapter. Furthermore we could then inject our concrete implementation at run time to our `PlayState` fx by letting the `init()` method take it as an argument.
