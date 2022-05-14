# JavaLab dependency analysis

## Class diagram

I'll only show parts of the class diagram that I find noteworthy in this section.

![IPostEntityProcessingService](http://www.plantuml.com/plantuml/png/fLDDIyD04BtlhnZm4Z65u4cab8ej8dWezE115ARPgHtSx8JTiLYA_zqrIIaXK2ZDPPPz7tblDvb6nQ3VbJCgDQZ0IbIb0ik1IcNn9dwnhp5W1OuzwQucaA0RCQPq62CyHAKWR9UrOqrBs1k0cy5sTdqF3L07AId6x0ahcg5Y3gyId4tmDj4RIXa8bHOIjL4VhBB38eUr4zHk96K7jEeDUkkeKHuL_PVyTjaVnJcEB7v6sjRjqW-1S_ApLmenwM8szygwow6TYRGISOG-HMXOQom9dl-hjen5wyx-SqbXomaSZ-78d4nWVpf5l-0BhwcpFDnCV--iMd56QBD6Zq6FPJfq_bDp89ir4vN5zsafbHbyBRrdz7PL57UtHP5RZ1koxwCFE7qwPdXJ5nhsZmpWmeHcIjwc_-iF)

AsteroidSplitter, could've been less specific and become an `EntitySplitter` which would then make it more abstract and easier to re-use. We might implement a `BulletSplitter` later.

![IEntityProcessingService](http://www.plantuml.com/plantuml/png/hP9FImCn4CNl-HG3NbPiOV4aKahzKyHROGyUH67SZ6umoOHajh0Klxkrg-GIKPRc5FWDxysRcLKIZD8xguG32CCBYx03RIArOjYhz6vym8WEhF4aGo3WA1qhrLfC2QwIK6IZRzXBPDiCISZ1JW6SbLbrUWa76YAtb59rXuvkKN063nojtkCj6tqewOD6eQR3GDNQYv7XXtvysr_trf8Kp3EPPfqwPiccu-UVJQqzkQ7KKmR7deRnGl4LMuBx4Mr6FUFV6efRimUB1UIgvH9sV_lcI1kB0yLIfaoE7agLZ-lhpHzFwlhYlAvd6bD7-cd-3Bz7LwKpWIb2XTLE4oeDT8AGMf7NlREV)

Despite the increase in features when comparing it to the [IntroLab](intro_lab.md), the internal coupling got lower. This is due to depending on abstractions (interfaces) instead of concrete implementations.

Despite that this is better, we still have few things that stick out when looking at the class diagram.

### LifePart knowing about Entity

![EntityParts](http://www.plantuml.com/plantuml/png/hLJVQnD147w_lsA9BsTD8Gg2Xb8ARP52YyMqeBGH9hkJt9gztNDtBYP8_tTpbqnkaxCcejU7PdxCDz_CjxjdWS1JcPk4N278YP4ZShbGsgCaxMmIfjeMu24NHbkaHO72USfSaaW38OXBIveMucSYnAkraMfBB_iYzZGc9tH5b1KoAF1pNuoD0teOrZuFIfTXxMkolcndNLtqnVtzrKLjW5BfgiCj-0fyvhIg8yh3Z_HgK05yGglGnq6FkPjXUbxD4_lnUmacf1z7tr1MQ2Fd38AjOnEagiUcw7BJvWOfS-hPPbZpkriKk2pQ_FuFMnYGrtQIF2NQ4lenI1JDBiJ9YMZyfwThlGhl98QGVe0SBu2WkovXmayiyhpG7ghpK8FLnJlEjER3Wk8cnjJtjsCnL2cnmMRS9gEDpJIlzHWZWcosZbk6O2ZhLwxQDil4nW8Ya5FqhCygnGMgyLOZe4fZIXozXa3d2samZO_BWPDBCB8qGCk472Pu1rEqw-CSfJ_7SizWlA8RDzDs4Ys97QnWLxJ-DR9wFSbeUxAosBOLIZJOrZw7-Q10LB7FEwhpMW6GlmSnEAQ_Nsn4aQdyGyTumFt3j9V6Ijowe7TkLEnA_o33F8PViC7F1TZjzmA9SRSmUKC7RR45tMuzRsvZigeM_7LUTyJHySEBj--hlsvXGDlXyQde5E0fFDgZJlnKLL-oy-A8PNMlz-vDhzTL439Km-ElebN45tvVMxuxZtQP4NDjWnpOERfMXnV5SXzGb9oXLMLkVW40)

In my effort to find a way to handling how much damage an entity should take depending on who attacked it, I created a dependency from `LifePart` to `Entity`. This was done after I gave up on the idea on only depending on an `EntityType` enum, which we can see from the class diagram that I forgot to delete again.

#### What could I have done instead?

I could've utilised the `Event` class that's currently not really being used, and created a `CollisonEvent`. Since these events are stored in the gameData, I could then have made a `CollisionPostEntityProcessingService` that would look at events of the `CollisionEvent` type and done the damage calculation there.

That's better I think since the `LifePart` has fewer reasons to change, and it would then only be responsible for managing the health less or equal to 0, deduct life part.
Furthermore the current implementation is prone to bugs, since we can only be attacked by one player each game loop iteration (each cycle), this would be avoided if we simply appended collision events.

### MyShapeRender being inside the Common project

I moved the draw behaviour for entities inside each entity, since I would argue that an entity would know what it was it self. However to reduce coupling, I inject the dependency as an argument inside the draw method, by specifying the interface `IShapeRender` instead of a concrete implementation. Futhermore I use the template pattern and supply a default `drawMethod` that children of Entity can then override, to reduce duplication of setting up the shape render.

This is all well and good, but the implementation of the adapter `MyShapeRender` should've been inside the `Core` module, that way we have encapsulated `com.badlogic` to only be a depedency for the `Core` module, making our code more friendly to change.
