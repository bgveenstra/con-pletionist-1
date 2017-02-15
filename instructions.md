<!--
Creator: SF Team (Alex White)
Location: SF
-->

![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)

# Rails 5 JSON API

### Why is this important?
*This workshop is important because:*
- Rails is a powerful JSON API building tool
- API mode is now built in to Rails 5

### What are the objectives?
<!-- specific/measurable goal for students to achieve -->
*After this workshop, developers will be able to:*

- Build a Rails API to CRUD a resource
- Use the `jbuilder` gem to generate JSON from templates
- Enable cross-origin resource sharing so a non-Rails front end can access your API

### Where should we be now?
*Before this workshop, developers should already be able to:*

- Create a Ruby on Rails CRUD app
- Create an Angular CRUD app using an external API

Meta-goals:

  * Practice finding and using documentation.
  * Practice picking out key words from project descriptions.
  * Explain pros and cons of using code generators.


## Steps to Create the con-pletionist App

For the conference-going completionist.  Conferences have many talks! This app helps track them. Responds to html and json format requests, with a permissive JSON API.

This repo holds the completed app solution.
[Link to completed app on heroku.](https://con-pletionist.herokuapp.com)


### Set Up Conferences

1. Generate a new rails project (called `con-pletionist`).  Skip turoboliks, use a postgres database, and skip Rails' built-in Minitest tests.

  <details>
    <summary>Stuck? Click to see the Terminal command to run.</summary>
    In your Terminal, run `rails new con-pletionist --skip-turbolinks --database=postgresql -T`
  </details>  

  **Next up:** explore scaffolding

2. In your Rails app, generate a [scaffold](http://guides.rubyonrails.org/v3.2.9/getting_started.html#getting-up-and-running-quickly-with-scaffolding) for a `conference` resource that includes the conference's name (a string) and location (also a string, for now). Carefully read the output in the Terminal. Scaffolding generates a **lot** of files.


  > While scaffolding will get you up and running quickly, the code it generates is unlikely to be a perfect fit for your application. You’ll most probably want to customize the generated code. Many experienced Rails developers avoid scaffolding entirely, preferring to write all or most of their source code from scratch.
  > - [Rails Guides: Getting Started](http://guides.rubyonrails.org/v3.2.9/getting_started.html)

3. Start your server, and rake your routes.

  <details>
    <summary>Stuck? Click to see some Terminal commands you'll need to get the server going.</summary>
    ```bash
    $ rake db:create
    $ rake db:migrate
    $ rails s
    ```
  </details>
  
  <details><summary>click to see `rails routes` output</summary>
  ```
          Prefix Verb   URI Pattern                Controller#Action
   conferences GET    /conferences(.:format)     conferences#index
               POST   /conferences(.:format)     conferences#create
    conference GET    /conferences/:id(.:format) conferences#show
               PATCH  /conferences/:id(.:format) conferences#update
               PUT    /conferences/:id(.:format) conferences#update
               DELETE /conferences/:id(.:format) conferences#destroy
  ```
  </details>

4.  Once you have your Rails server running, spend 5 minutes interacting with the site. Try all CRUD actions for conferences. Try visiting `/conferences.json`.

5. Spend 10 minutes looking through the code Rails generated.  Pay particular attention to `app/controllers/conferences_controller.rb`, and answer the following questions with a partner:

  * In the conferences controller, what does the line `  before_action :set_conference, only: [:show, :edit, :update, :destroy]` do?

  * Taking a hint from the generated comments in the conferences controller, visit the `/conferences.json` endpoint in your browser. What do you see?

  * In the conferences controller, what are the `respond_to do |format| ... end` blocks for?

  * What are the `format.html` blocks doing?  Which views are they rendering?

  * What are the `format.json` blocks doing?  Which views are they rendering?
  


  **Next up**: build JSON with `jbuilder`
  
  **Comment-in** the jbuilder gem.
  
  ** It won't work.  Make sure the format for the routes is processed at JSON.**    **Make sure the format for the routes is processed at JSON**

6. The JSON views are using a gem called `jbuilder`, which might have noticed in the file extension `.json.jbuilder`.  Look over the first example input and output in the [`jbuilder` docs](https://github.com/rails/jbuilder) to see some of how `jbuilder` creates structured data.

7. Edit `views/conferences/index.json.jbuilder` and/or `views/conferences/show.json.jbuilder` so that both JSON views include a field for the url of each conference. Use url helpers to generate the correct url for each conference, and set the format of the url to JSON. Hint: start by looking at each of these files. 

  <details>
    <summary>Stuck? Click to see suggestions about where to look for more information.</summary>
    * search for "Rails url helper"
    * search for "Rails url helper JSON format"
    * revisit the example in the `jbuilder` documentation
  </details>

### Incorporate Talks

  **Next up**: scaffold talks

8. Generate a scaffold for a `talk` resource. Talk information should include the talk's title, the speaker's name, the start time, and the end time (use the `datetime` type to capture dates and times).  Also add an attribute for the conference a talk belongs to. If you're not sure how to generate a scaffold for an association in rails, do a search for that.

  <details>
    <summary>Click to see the Terminal command used to generate the scaffold in the solutions.</summary>
    ```bash
    $ rails g scaffold talk title:string speaker_name:string start_time:datetime end_time:datetime conference:belongs_to
    ```
  </details>

9. Get your rails server running again with talks, and spend 2-5 minutes using the site. Try CRUD operations on talks.

  **Next up**: solidify the association

10. Look at the list of files Rails generated or updated. Where would you expect to see the association between conferences and talks reflected?

11. Wait to make changes, but explore the following files or directories and see how or if the association is reflected in each. When you're finished with a file or directory, click the file or directory name to compare notes.

  * <details><summary>views/talks/</summary>
    form partial includes a conference id (and a cool datetime form element!); show and index html templates display the conference; show and index JSON templates include the conference id</details>
  * <details><summary>config/routes.rb</summary> routes for talks and conferences are both included, but talks aren't nested in conferences </details>
  * <details><summary>app/controllers/talks_controller.rb</summary> the conference id is brought in as a permitted parameter in `talk_params` </details>
  * <details><summary>app/models/talk.rb</summary> `belongs_to :conference` is set up! </details>
  * <details><summary>app/models/conference.rb</summary> the association is incomplete on this side; you'll add `has_many :talks` here </details>
  * <details><summary>db/migrate/</summary> there's a new migration file to create the talks table, and it includes a `t.belongs_to` for the conference foreign key </details>
  * <details><summary>db/schema.rb</summary> since you had to run `rake db:migrate` to get talks working, there should be a talks table here with the foreign key set up </details>


12. Create a one-to-many association by coding the fact that a conference has many talks into your conference model. Also make sure that a conference's talks are destroyed when the conference is destroyed.  Why do you suppose the scaffold left out the `has_many` code? Discuss with others until you're satisfied with an answer.

  **Next up:** more complex custom jbuilder view

13. Update the JSON show view for single conferences so that conference JSON lists all of the conference's talks in an embedded array.  Each talk should include a url link to its individual JSON page, but they don't need to include times they were created or updated.

  **Next up:** seeds!

14. Add seed data to your project to create at least 2 conferences and 5 talks. Test your CRUD methods in the Rails console.

  <details><summary>Click for tips for seeding times in Rails.</summary>
    * you can use `Time.current` to get a time with time zone information (the default time zone is UTC)
    * (you can set the time zone with `Time.zone=` or `config.time_zone`)
    * you can use methods like `2.5.hours` to create amounts of time that can be added or subtracted with other times
    * you can also use methods like `1.days.from_now.at_noon` to create times
  </details>

15. Seed your database, and run through your app to confirm that your JSON views are working as expected.

**Note:** At this point, with JSON view templates and routes, use Angular on the front end instead.  (Details coming soon!)  For now, though, we'll move in a different direction and allow any front end to access the API.

### Open your API

**Note:** Rails generally assumes that only the Rails front-end will be consuming the data stored in the Rails project database. The API of this site will be open to other requesters, though. In fact, we'll open it up to any request origin that wants to use or change the data here.

17. Use `cURL` to test access to the following routes on your Rails site:
  * `GET /talks`
  * `GET /conferences/:id` (for some id you know exists on your site)
  * `POST /talks`
  * `DELETE /talks/:id` (for some id you know exists on your site)

  <details><summary>Click for suggestions about where to find out how to do this!</summary>
  * search "curl json format rails"
  </details>

  Write your `cURL` commands down to use again later.

  <details><summary>Stuck? Click to see a sample verbose `cURL` command to get JSON for `GET /conferences/3`. (You'll need to modify it!)</summary>`$ curl -v -H "Accept: application/json" -H "Content-type: appliction/json" -X GET https://con-pletionist.herokuapp.com/conferences/3`</details>

  <details><summary>Stuck? Click to see a sample verbose `cURL` command for `POST /talks`. (You'll need to modify it!)</summary>`$ curl -v -H "Accept: application/json" -H "Content-type: application/json" -X POST -d ' {"talk":{"title":"The Future of Kia Automobiles","speaker_name":"Chip Cilantro"}}'  https://con-pletionist.herokuapp.com/talks`</details>

18. Do you see errors or unexpected results when you run your `cURL` commands?

  **Next up:** change the CSRF protection strategy to allow cross-site requests

19. Rails provides a lot of [security features](http://guides.rubyonrails.org/security.html).  One such security feature is a token that Rails places on every page to protect from Cross-Site Request Forgery (CSRF).   Unfortunately, this protection throws an exception and doesn't let anyone else use our api.  Configure Rails to protect the site from forgery with the `null_session` strategy instead of with an exception.

  <details><summary>Click for a suggestion about where to find more information.</summary>
    * search "Rails CSRF null_session"
  </details>

20. Try your `cURL` commands again.

  **Next up:** configure CORS headers with `rack-cors`

21. Cross-Origin Resource Sharing (CORS) is a standard for sharing resources across domains. The gem `rack-cors` can help configure Rails apps to use CORS. Follow the [`rack-cors` documentation](https://github.com/cyu/rack-cors) to add this gem to your project and configure it.  Use the Rails 4 example linked from that documentation.

22. Try your `cURL` commands again. All of them should be successful!



## Extra Challenge Ideas

These bonus challenge ideas are not closely related to the fundamentals of this lesson. Please skim over them and feel free to attempt any you find interesting.

### Miscellaneous User Experience Improvements

Clean up this site's dismal front end.  Slightly less dismal solution provided.

1. Add bootstrap css to the site, and make the following minor changes in views:
  * apply appropriate bootstrap classes to tables
  * apply appropriate bootstrap classes to forms
  * put the content on each page into a container

2. Modify HTML views so that instead of `#<Conference:0x007fa49430bc78>` or `#<Talk:0x007359ab3e2391>` the user sees the conference name or talk title.

3. Format the displayed times on your site so they're easier to read.


### HATEOAS

Level up knowledge of REST with some research, and practice `jbuilder`. Solution not provided.

1. HATEOAS, like RESTful routing, is a part of the REST convention. HATEOAS makes it easy to discover resources on a site.  It's less frequently used than RESTful routing but still very common.

2. Update your JSON views to include links implementing (or approaching) HATEOAS.  


### Nest Talk Routes in Conferences

More conventional treatment of dependent resource, talks.  Solution provided.

This bonus involves changing many files throughout the app. It will be time-consuming. If you choose to work on it, start a new branch. Consider changing over routes one or two at a time instead of doing them all at once.

1. Nest the talk routes inside conference routes, and `rake routes`.

  ```ruby
  resources :conferences do
    resources :talks
  end
  ```

2. Update the `talks` form partial so it no longer takes in a conference id.

3. Update the `talks` html views to take the new routes, paths, and urls into account.

4. Update the `talks` JSON views to take the new routes, paths, and urls into account.

2. Update your talks controller to take the new route structure into account (by looking up the conference for actions that need it).  Make sure actions are modifying the conference if needed, too.

3. Update your talks controller html format responses to reflect the change in routes.

4. Update your talks controller JSON format responses to reflect the change in routes.
