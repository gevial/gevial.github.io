---
title: 10 принципов построения инфраструктурных сервисов
date: 2016-12-24 22:17:38
tags: [book, soa]
---
Продолжаю постить выдержки из книги **"Web Operations"** (ISBN 978-1-449-37744-1). На этот раз речь пойдёт о требованиях к инфраструктурным сервисам (Service-Oriented Architecture).

In our exploration of service-oriented architecture and configuration management, we identified 10 core principles of a good infrastructure service.

#### It should be modular.

*“Do one thing, and do it well.”*

In an SOA, each service is small — it does one thing, and allows other services to consume it. The complexity budget for any given service is tiny—the cost of integrating services is pushed up the stack to the application developer. By focusing each service in a narrow band, the services become easier to manage, develop, and test.

The same is true when thinking about infrastructure services. By keeping the total space in which a given service operates small, you reduce its own complexity, and make it easier for others to reason about your behavior.

#### It should be cooperative.

*“It takes a village.”*

When you build primitive services exposed via network APIs, you encourage everyone to cooperate with you, rather than reimplement the same functionality. Each service must be designed to cooperate with others and have as few expectations as possible about the way in which it will be used.
The cooperative nature of the service ensures that, as more people use it, the service itself becomes more useful. For infrastructure services, this nature is critical — as each piece of the infrastructure becomes integrated with the service, the ways in which they can cooperate with each other explode exponentially, in ways the original service authors could never have predicted.

#### It should be composable.

*“It should be ready for anything.”*

Once you have a bunch of nicely modularized services, you begin to compose them together into more complex applications. This is the moment where service orientation really begins to shine: as each primitive problem is solved, new applications can simply rely on it to provide the functionality required.

Each component of the infrastructure gets a modular API, and you compose them together to automate the entire infrastructure.

#### It should be flexible.

*“It should be capable of doing whatever is required.”*

No matter what your problem is, it’s likely that you can solve it with a high-level programming language. Given even the most obscure and complex of policies, you can probably write a program to automate it — you are limited only by your knowledge and the facilities available in the language.

#### It should be extensible.

*“When something new is encountered, it’s easy to extend.”*

When you encounter a problem the language has never seen before, such as a new protocol you need to speak, you can write a library to resolve it. This is a side effect of the language being general purpose—because it doesn’t pretend to understand the sorts of problems it might be called upon to tackle, it behooves the designers of these languages to make them extensible enough that developers in the field can scratch their own itch.

#### It should be repeatable.

*“No matter how many times you do it, it works the same way.”*

When you write a piece of automation around a process, you cause that process to become repeatable. Given the same set of inputs, and the rest of the system functioning properly, you are going to get the exact same results. A side effect of being repeatable is being consistent—once you can rely on the results, you can also expect that the results will be correct.

#### It should be declarative.

*“It’s about what you want, not how to do it.”*

Rather than talk about how to do something, you should declare what you want done and let the tool determine how to do it on your behalf. For example, in Cfengine 2:
```
packages: sudo
  action=installed
```
would make sure that sudo is installed. This is opposed to the following, which focuses on how you install sudo
```
apt-get install sudo
```
The declarative syntax has an additional side effect: for the things the tool knows how to manage, you can think about what you want the system to be like (the policy) and leave the process to the tool.

#### It should be abstract.

*“It takes care of the details for you.”*

Under the hood, configuration management tools know how to map the description of the state you want a resource to be in to the mechanism the system has to fulfill it. They transform a package statement such as the one in the preceding bullet to the apt-get statement through auto-detecting the appropriate mechanism for your platform.

#### It should be idempotent.

*“It takes action only if it needs to.”*

Each resource is responsible for ensuring that it is in the proper state, and that means action is taken only if the resource isn’t already properly configured. If the entire system is properly configured, no action will be taken.

#### It should be convergent.

*“It takes care of itself and relies on other services to do the same.”*

Each resource is responsible for ensuring that it is in the proper state. With each resource acting as an autonomous actor, the system as a whole operates in a convergent manner; over time, each small act brings the system fully in line with the overall policy. In addition to the declarative syntax, the convergent nature of configuration management tools allows system administrators to focus on the policy, not the process.
