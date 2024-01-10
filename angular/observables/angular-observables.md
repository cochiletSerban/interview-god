<h1 align="center">Observables: RxJs deep dive</h1>
<img src="img/title.png" style="display:block; margin:auto;" width="500px">

<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 15px 0 0 5px;">#hotNcold</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#shareReply</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#intersectionObserver</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#RxJSInterop</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#errorStream</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#signals</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#switchMap</span>

---

imperative vs declarative programming

- hot / cold observers / share reply
- generating error streams
- switch map / popular operators
- callback to promise to observer to ngrx component state to observer to signal

# Introduction

In the ever-evolving landscape of Angular development, proficiency in RxJS is a prerequisite for seasoned developers. In this comprehensive exploration of advanced RxJS concepts tailored for Angular developers with a wealth of experience. We embark on a journey through the intricacies of Observables, demystifying the dichotomy between Hot and Cold Observables, and arming you with the insights needed to wield them with precision. From discerning the nuances of Promises versus Observables to bridging signals with RxJS interoperability, this series is designed to elevate your understanding and application of these crucial concepts.

The journey continues with a deep dive into the generation of error and ViewModel streams, providing you with the tools to fortify your application's resilience. We then unravel the secrets behind popular RxJS operators, offering practical examples that illuminate their application in real-world scenarios. Finally, we explore the paradigm shift from imperative to declarative programming, paving the way for optimized and elegant Angular solutions. This series is your key to mastering the advanced facets of RxJS, enabling you to create Angular applications that are not just functional but truly exceptional.
