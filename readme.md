# Prowl Ideas
Ideas pile for Prowl ([tutorial](./learn-fast)).

Prowl is a purely-functional, statically typed, modular, concatenative relational programming language. 

Main inspirations include: 
- Stack Languages (Kitten, Factor, Joy)
- Functional Languages (OCaml, Haskell)
- Relational Languages (miniKanren)
- Kleene Algebras (Vinegar, Oniguruma Regex)

Our look is highly original, though loosely inspired by  SML, Reason, Factor, and Regex. 
```
rel n fac -- 
  (n == 0) 1
  : n * (n - 1) fac
```
Alternatively: 
```
rel fac -- (
  as 0 -> 1; 
  as n if n > 0 -> n * (n - 1) fac
)?!
```

Check out [learn-fast](./learn-fast) for more. 
An [interpreter](https://github.com/UberPyro/prowl) exists for a much older, much less cool version of the language. 

## Links, Inspirations, Motivators
- [Stack Language Introduction in Kitten](http://kittenlang.org/tutorial/)
- [Why Concatenative Programming Matters](http://evincarofautumn.blogspot.com/2012/02/why-concatenative-programming-matters.html)
- [Expressive Static Typing for Stack Languages](https://www2.ccs.neu.edu/racket/pubs/dissertation-kleffner.pdf)
- [Thun Essays](http://joypy.osdn.io/notebooks/index.html)
- [Relational Programming - "This Is It"](https://matt.might.net/articles/microkanren/)
- [Relational Programming in miniKanren](https://scholarworks.iu.edu/dspace/bitstream/handle/2022/8777/Byrd_indiana_0093A_10344.pdf)
- [Relation Algebra](https://en.wikipedia.org/wiki/Relation_algebra)
- [Category of Relations](https://ncatlab.org/nlab/show/Rel)
- [Concatenative Error Handling in Vinegar](https://github.com/catseye/Vinegar)
- [Recursion Schemes](https://blog.sumtypeofway.com/archive.html)
- [Generalizing Monads to Arrows](https://pdf.sciencedirectassets.com/271600/1-s2.0-S0167642300X00299/1-s2.0-S0167642399000234/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEKj%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJHMEUCICuCKU2YbdiF7c%2B7T0xxiEPfqcLSfP41A1TgmtdD60DWAiEAkuA9DkZuXcWJVvziPL9OPZMnHUBFQ3AHMJg18QQWi0Yq0gQIIRAFGgwwNTkwMDM1NDY4NjUiDHuXzWB%2BC%2Fitg3%2BbciqvBEjnCl1xv%2FCRwqNe13pmyGjng5f3YjWzijunXaPCIhqZ7OlFTAYsiLKs0QP0EOvx3LIBl9Cl4dbZhwk80eM%2BLRZGEwf%2FiKRRwad1ZdBMS%2Bh%2FX4HK7YCsm2%2F%2BBNlrrvuf3CRiHLHvXQj0khJWXclWdXTUh388uG73%2Fnyw5TVjiJdEJxQENULSqfOrw0bzxVPgtEIfLkqfb%2B%2BK%2F1F50w9ZZJwbCwdwCMKGd7L4cTKByXfQcNl%2B%2FBIn7QMBkWhySMW34vMGyIJdCHTkN0BCnw8SpBur5RX%2FTjCf7ulHGJ%2FovxwIx5Ne2cZk%2FXKynuMeKqhM2C0Q8%2FC0SZ6NNMp1%2FK2DrEt0w5YkDLoQhovwAdHe07V0QUJdB2PqUFeT%2F1OlFcnuVSF0zs0aSdfF5n3UbaTOsnJ5rzKjoruzLVZjA0NsvTOd8BwFYY4gdKH9NJ6OnrCUcH%2Bw%2FTpWKjdHV5L7H7c%2BxDVSYeIcMRCkWi7G7NmAkbof1arRCJeNHYz5w5%2BD37pgDz0%2BmBoAFU9dr0HzGgBjcIfzeO9koFs1aPFF2qubZ3Cb8bBTU5kAhJKImM%2Fg0SKmRt%2Fdqh1qC7uu56LgS1WRsmxLlpsNk4JaaJdvMhm0lVJi1F3gbpPk045fI7kp8q54SVe2nxa6qGKFPFPh7o4EJdpfXLNYznXrwhVrWcXrYBfyjlOP1G5vB0Pr%2B6BQxJrh4CmZ4ytXsfmRZQ6bQcErnF5nO7bb6pQHN0%2F28%2FcctWMwraH7lwY6qQEJeSY787HmpuZHUlsRPdhLK8N%2Ba6DIQJ1MorBWQ4hU%2BCJnU55pvr9JcUYtw%2FYyQJs28vJM8Qaq4cEMyQyOejp6lu%2BYaqSJfIzMMc%2BlIvjIJImvxwtlFqbPCSTar9gLXURg3i5YIKOfP1K15gYPObfRUw3yevD6lSh6UYUt6rxKyelz%2BAmncwd3NL2%2F%2FgpN6yjPUhmE628f2BtUDwigJPYRa6qijAEuOAyk&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20220819T011532Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTY6YKL5PU5%2F20220819%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=f19efbae346bc3a077a56216f6b418dc0d72b72bc226caaa43187bd2dc78daeb&hash=0ce552475c07073ae40e1039064fc695a58491f496f9b1057a57348b41ddc5e0&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0167642399000234&tid=spdf-40a70b11-af05-4bf5-8015-00e53b10b57e&sid=0367d7192bdae94d3e88738-27dab3af9783gxrqa&type=client&ua=4d550609525b06585704&rr=73cf09e84ad66398)
- [Understanding Arrows](https://en.wikibooks.org/wiki/Haskell/Understanding_arrows)
- [MixML Module System](https://people.mpi-sws.org/~rossberg/mixml/)
- [Modular Implicits](https://arxiv.org/pdf/1512.01895.pdf)
- [Reason Syntax](https://reasonml-old.github.io/guide/ocaml/)
