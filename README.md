Local Time is a Rails engine with helpers and JavaScript for displaying times and dates to users in their local time. The helpers render a `<time>` element and the JavaScript swoops in to convert and format. The helper output is ideal for caching since it's always in UTC time.

---

####Example

```ruby
> comment.created_at
"Wed, 27 Nov 2013 18:43:22 EST -0500"
```

```erb
<%= local_time(comment.created_at) %>
```

Renders:

```html
<time data-format="%B %e, %Y %l:%M%P"
      data-local="time"
      datetime="2013-11-27T23:43:22Z">November 27, 2013 11:43pm</time>
```

When the DOM loads, the content is immediately replaced with a local, formatted time:

```html
<time data-format="%B %e, %Y %l:%M%P"
      data-local="time"
      datetime="2013-11-27T23:43:22Z"
      title="November 27, 2013 6:43pm EDT"
      data-localized="true">November 27, 2013 6:43pm</time>
```

*(Line breaks added for readability)*

#### Local time and date helper

```erb
<%= local_time(time) %>
```

Format with a strftime string (default format shown here)

```erb
<%= local_time(time, '%B %e, %Y %l:%M%P') %>
```

Alias for `local_time` with a month-formatted default

```erb
<%= local_date(time, '%B %e, %Y') %>
```

To set attributes on the time tag, pass a hash as the second argument with a `:format` key and your attributes.

```erb
<%= local_time(time, format: '%B %e, %Y %l:%M%P', class: 'my-time') %>
```

To use a strftime format already defined in your app, pass a symbol as the format.

```erb
<%= local_time(date, :long) %>
```

`I18n.t("time.formats.#{format}")`, `I18n.t("date.formats.#{format}")`, `Time::DATE_FORMATS[format]`, and `Date::DATE_FORMATS[format]` will be scanned (in that order) for your format.

Note: The included strftime JavaScript implementation is not 100% complete. It supports the following directives: `%a %A %b %B %c %d %e %H %I %l %m %M %p %P %S %w %y %Y %Z`

#### Relative time helper

```erb
<%= local_relative_time(time) %>
```

Displays the relative amount of time passed using preset time and date formats that vary with age. The available types are `date`, `time-ago`, `time-or-date`, and `weekday`. Like the `local_time` helper, `:type` can be passed a string or in an options hash.

```erb
<%= local_relative_time(time, 'weekday') %>
<%= local_relative_time(time, type: 'time-or-date') %>
```

**Available `:type` options**

* `time-or-date` (default) Displays the time if it occurs today or the date if not. "3:26pm" or "Apr 11"
* `time-ago` With age, the descriptions transition from {quantity of seconds, minutes, or hours} to {date + time} to {date}. Aliased as `local_time_ago`.
* `date` Short date-only format. Includes the year unless `time` is within the current year. "Apr 11" or "Apr 11, 2013"
* `weekday` Displays "Today", "Yesterday", or the weekday (e.g. "Wednesday") if the time is within a week of today.

The `<time>` elements are updated every 60 seconds.

#### Time ago helper

```erb
<%= local_time_ago(time) %>
```

Alias for `local_relative_time` using the 'time-ago' type. Examples (in quotes):

* Recent: "a second ago", "32 seconds ago", "an hour ago", "14 hours ago"
* Yesterday: "yesterday at 5:22pm"
* This week: "Tuesday at 12:48am"
* This year: "on Nov 17"
* Last year: "on Jan 31, 2012"

#### Installation

1. Add `gem 'local_time'` to your Gemfile.
2. Run `bundle install`.
3. Add `//= require local_time` to your JavaScript manifest file (usually found at app/assets/javascripts/application.js).

#### JavaScript events and library compatibility

The included JavaScript does not depend on any frameworks or libraries, and listens for a `DOMContentLoaded` event to run initially. It also listens on `document` for `page:update` if you're using Turbolinks and `ajaxSuccess` if you're using jQuery. This should catch most cases where new `<time>` elements have been added to the DOM and process them automatically. If you're adding new elements in another context, trigger `time:elapse` to process them.

#### JavaScript API

`relativeDate`, `relativeTimeAgo`, `relativeTimeOrDate`, `relativeWeekday`, `run`, and `strftime` methods are available on the global `LocalTime` object.

```js
> LocalTime.relativeTimeAgo(new Date(new Date - 60 * 1000 * 5))
"5 minutes ago"

// Process <time> tags. Equivalent to dispatching a "time:elapse" Event.
> LocalTime.run()

> LocalTime.strftime(new Date, "%B %e, %Y %l:%M%P")
"February 9, 2014 12:55pm"
```

#### Version History

**unreleased**

* Support future relative times (maintaining misnomer `local_time_ago`)
* Set default type for `local_relative_time`

**1.0.0** (April 12, 2014)

* Added `local_relative_time` helper with several built in types
* Allow `:format` (and `:type`) option as a bare string or value in hash
* Added `relativeDate`, `relativeTimeOrDate`, `relativeWeekday` and `run` to the API
* Dropped ineffective `popstate` event listener
* Now in use at [Basecamp](https://basecamp.com/)

**0.3.0** (February 9, 2014)

* Allow :format option lookup in I18n or DATE_FORMATS hashes [Paul Dobbins]
* Expose public API to JavaScript helpers

**0.2.0** (December 10, 2013)

* Prefer `textContent` over `innerText` for Firefox compatibility
* Added `options` argument to `local_time_ago` helper

**0.1.0** (November 29, 2013)

* Initial release.
