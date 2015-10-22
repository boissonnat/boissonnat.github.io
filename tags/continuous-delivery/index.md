---
layout: tag
tag: continuous-delivery
---

Following the _Lean software development_ principle, you need to be able to deliver your products continuously.
Coupled with [continuous integration](/tags/continuous-delivery), [Git](/tags/git) and [Capistrano](/tags/capistrano), here is how it
works in my [ruby](tags/ruby) world :

1. Commit your changes 
2. Run the tests locally
3. Push on the origin repository
4. The CI tool will start automatically to :
    - Run the tests
    - Merge in production branch
    - Deploy, through capistrano, to the server (staging or production)
    
Choosing to deploy on production server or on staging server makes difference between continuous deployment and continuous delivery. 
In continuous delivery you deploy manually the application on the production server after the acceptance test (made on the staging server), while
in continuous deployment you push automatically to production server after acceptance tests. I always try to use continuous deployment, but this implies to be able
to run acceptance tests automatically and, in some rare cases, this step is not feasible.