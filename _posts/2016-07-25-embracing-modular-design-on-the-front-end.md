---
layout: post
title:  "Embracing Modular Design on the Front End"
date:   July 25, 2016
categories:
  - Design Patterns
tags:
  - Front-End
  - NPM
comments: true
---
In recent years, there has been a large shift in systems architecture due to the containerization and virtualization through technologies such as Docker as a means to run more efficiently in cloud based environments. Arguably, some of the same architecture principles and design patterns have spilled over into application development through componentization of front end web controls and back end services. As a result, package managers have become increasingly popular due to their ability to encapsulate specific library functionality and contribute to the reuse of said libraries.

### What is a Package?

Front end frameworks such as Angular 2, React, Polymer, and the Web Components standard all share the opinion that the **components** on a web page should be directly tied to an encapsulated piece of code that controls that particular component. Doing so allows for a more modular front end development process, which in turns, makes code more reusable and decoupled from the rest of the page. Components and other libraries that are self contained are great examples of what can be considered a package. Sharing these packages can be achieved by the use of a package manager, which will provide functionality for obtaining, versioning, and updating any given package.

### Why a package manager?

Modern web development has embraced the utilization JavaScript modules as a means to achieve **modular** design, which promotes **separation of concerns**, **reusability**, and the **single responsibility principle** to a degree. Packages (modules) are very easy to use and are resolved and included into the project at build time, rather than run time. They’re also not a new concept, and most languages, frameworks, and platforms have utilized them for years. I.e.

- C# -> NuGet
- Ruby -> gems
- Python -> pip
- PHP -> Composer
- JavaScript -> npm, bower, jspm
- Windows -> Chocolatey
- Linux -> apt-get
- Mac -> Homebrew
- [etc...](https://en.wikipedia.org/wiki/List_of_software_package_management_systems){:target="_blank"}

### How is this different than a CDN?

A CDN, or Content Distribution Network, is a remote content delivery source that will serve application dependencies. Unlike a package manager, these dependencies are resolved at run time rather than build time. This generally requires additional precautions to account for CDN servers being down such as hosting a local copy of the script as a backup.

### What problems do packages solve?

Package managers provide an easier and more reliable alternative for front-end (and non) libraries to CDN's. In the context of NPM, and most other package manager systems...

- The use of semantic versioning
  - Allows each application to upgrade to the latest version when it is ready.
  - Easier to regression test, due to an opt-in package model.
  - This is only a benefit if your CDN does not support semantic versioning (or any versioning).
- Build time dependency resolution
  - Dependency resolution is no longer a point of failure in a production environment.
- Easily see and resolve library dependencies at development time.
  - Dependency management is built in to NPM.
  - Spend less time finding dependencies and understanding how a disconnected system works.
  - Spend more time adding features and fixing bugs.
- Provide a better foundation for developing libraries as individual, self contained, interoperable pieces.

### Where should the packages be hosted?

NPM (node package manager) is the de facto standard for hosting JavaScript packages. It was created in 2009 to help JavaScript developers easily share packaged modules of code. NPM, to date, houses over 250,000 reusable packages and is the largest code registry in the world and is an extremely easy way to utilize and develop reusable code.

More Information can be found at [NPM](https://www.npmjs.com/){:target="_blank"}

Other notable package managers for JavaScript include *bower* and *jspm*, however, NPM is by far the most popular. Additionally, bower and jspm package managers are obtained through npm. (`npm install -g bower`)

### Can we host NPM locally?

Yes, but for a much much higher price. The question becomes not can we, but should we. NPM has grown as an open source ecosystem that promotes code reuse on a multitude of different projects. Most, if not all, of the projects that are hosted publicly do not have any proprietary logic to any particular company's system (unless, perhaps, it is an integration library to allow a customer to integrate with their system)

For packages that need to be private due to them containing specific business logic, NPM offers private repository hosting through their Solo and Organization plans.

Perhaps if you have the personnel, system, and monetary resources required to maintain a private npm registry, then a self-hosted version of npm enterprise may not be a problem. However, this is costly and most small to medium sized business should be fine with the Solo and Organization plans provided by npm.

More pricing information can be located at [NPM Pricing](https://www.npmjs.com/pricing){:target="_blank"}

### To open source or not open source

While open source is a separate topic from JavaScript modules and packages, the question will eventually come up since they are closely related. If your company does not currently contribute to open source, perhaps you should consider it. If you're making the move to use packages and host them on NPM, then this is a low hanging fruit that can easily be captured to benefit your company in a big way. At some point, you will need to decide whether your packages will be public or private.

So, should we open source our company's packages? **Some packages are fit for open source and some that are not**. Common utilities that are used on a daily basis that do not contain a company's proprietary “secret sauce” are great candidates for open source. To elaborate further, packages that a company receives no benefit by maintaining their own private version of it should probably be company sponsored public repositories.

**Benefits to open sourcing**

- Making utilities open source can offset some of the maintenance of these utilities
- Other people will help you for free. There are literally thousands of developers out there who are passionate about open source. Leverage this fact.
- Open sourcing libraries will force you to use a modular approach.
- Modules are kept lightweight because complex and unnecessary logic should be omitted. Public projects should not have business logic specific to your business.
- Open source projects sponsored by company's make a company more appealing.
  - StackExchange sponsors [Dapper](https://github.com/StackExchange/dapper-dot-net){:target="_blank"} and [Opserver](https://github.com/opserver/Opserver){:target="_blank"}
  - Google sponsors [Angular](https://angularjs.org/){:target="_blank"} and [Angular2](https://angular.io/){:target="_blank"}
  - Facebook sponsors [React](https://facebook.github.io/react/){:target="_blank"}
  - Netflix sponsors [many tools that they use internally](https://github.com/Netflix){:target="_blank"}
- Your company earns a reputation as an innovative leader in the community
- You will be able to hire the best engineers. In modern years, open source has become a great resource for hiring talent.
- You will get more out of your best engineers
- You open the door to forging partnerships with other open source companies.
- You will learn how to use open source more efficiently.
- Project documentation is easier to find when it's kept with the source code.
- You give back to the community.

Packages such as Angular or React utilities (i.e. error handling, CSRF, persisting state, accessing browser `localStorage` or session, attaching http headers) or anything else where no direct benefit is gained by maintaining a private version of are probably good candidates for open source.

**tl;dr:** If a library is general purpose, contains no business specific logic, and yields no direct benefits to maintaining a private version, then it is a good candidate to consider open sourcing.
