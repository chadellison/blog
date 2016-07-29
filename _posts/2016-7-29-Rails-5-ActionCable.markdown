---
layout: post
title:  "Rails 5 Action Cable"
date:   2016-07-29 22:17:38 -0700
categories: jekyll update
---
Texas Holdem using Rails 5 action Cable

Intro

One of the defining features of Rails Five is Action Cable. Action Cable allows one to integrate web sockets into a rails app for realtime updates. Most examples of Action Cable that I came across were chat applications. I wondered what it would be like to use it to build something more sophisticated, something like Texas Holdem. Texas Holdem requires several components that are available to all players (i.e. the flop, turn, river, blinds, pot, etc.) as well as components that only a particular user can see; namely, the pocket cards. With the combination of learning Action Cable and building the game logic for Texas Holdem (I did not use any libraries), I knew this would be a great way to challenge myself, and have fun doing it!

Some of the Challenges I came across
I knew that this project would require a lot of trial and error. Because it is a new technology, there aren't many online resources. I got the same 10 - 15 results when I googled anything with the name "action cable". Moreover, I'm not aware of how to feature test web sockets, so every time I added a feature, I would simply have to log in as two different users and click around to make sure I didn't break anything.

On the Technical Side
The default client side for Action Cable is CoffeeScript. I had never worked with it before, but it is similar enough to JavaScript that anyone with an intermediate level of JavaScript knowledge can work with it. The room.coffee file comes with some default methods that allow the client to both send and receive content:

{% highlight ruby %}
App.room = App.cable.subscriptions.create "RoomChannel",
  connected: ->
    # Called when the subscription is ready for use on the server

  disconnected: ->
    # Called when the subscription has been terminated by the server

  received: (data) ->
    # Called when there's

  speak: (message) ->
    @perform 'speak', message: message
{% endhighlight %}

For the server side, there are "channels". Channels function similarly controllers, providing access to models and database. It too comes with some out of the box methods to connect to the client side. Rails Five also has something called jobs. One of the benefits of jobs is that they abstract some of the broadcasting logic out of the channels. Here is an example of how I used one:
{% highlight ruby %}
class GameCardJob < ApplicationJob
  queue_as :default

  def perform(game_card)
    ActionCable.server.broadcast "room_channel", game_card: render_game_card(game_card)
  end

  private
    def render_game_card(game_card)
      ApplicationController.renderer.render(partial: "game_cards/game_card", locals: { game_card: game_card })
    end
end

{% endhighlight %}

The Awesomesauce of Action Cable
Action Cable is fairly intuitive and easy to work with--at least compared to socket io with node. I've been working on this app for about two weeks and here are some of the things I've been able to do:

(screen shot)

All players see the game cards while each player only sees their unique pocket cards. I've also highlighted the player whose turn it is in red and ensured that the action buttons (check, bet, fold) only appear on the client's screen if it is that client's turn. Too add to this, I also made AI players so that a user can play with only other users, only AI players, or a combination of the two.

Concluding thoughts

ActionCable is currently my first choice for all future web socket endeavors. There are several tutorials that are a good way to get started (here is a chat room app from DHH). The way that I learned best, however, was to just play around and experiment.
