---
layout: default
title: Commenting functionality for the Rails Girls app
permalink: commenting
---
# Комментарии для приложения Rails Girls
*от Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)*

Мы начинаем добавление воможности комментривания идей в вашем *railsgirls* приложении.

Инструкции по установке rails и создании приложения идей могут быть найдеты [здесь](/app)

## Шаг 1: Добавление гема foreigner

Добавьте в Gemfile
{% highlight ruby %}
gem 'foreigner'
{% endhighlight %}

Остановите сервер, если он запущен, и запустите в терминале
{% highlight sh %}
bundle install
{% endhighlight %}

## Шаг 2: Создание скаффолда комментария

Создайте скаффолд комментария с именем комментатора, содержимым комментария и связью на таблицу ideas(idea_id).

{% highlight sh %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

## Шаг 3: Добавление внешнего ключа для связи

Добавьте в миграцию связь через внешний ключ. Откройте db/migrate и найдите файл, оканчивающийся на 'create_comments.rb'. После строки
{% highlight ruby %}
t.timestamps
end
{% endhighlight %}

добавьте
{% highlight ruby %}
add_foreign_key :comments, :ideas
{% endhighlight %}

Теперь запустите миграции, напечатав в терминале
{% highlight sh %}
rake db:migrate
{% endhighlight %}

Запустите сервер:
{% highlight sh %}
rails s
{% endhighlight %}

## Шаг 4: Добавление связей в модели

Вы должны убедиться, что Rails знают о связи между объектами(ideas и comments).
Одна идея может иметь множествоо комментарией, и мы должны убедиться, что модель Idea знает об этом.

Откройте app/models/idea.rb и после строки
{% highlight ruby %}
class Idea < ActiveRecord::Base
{% endhighlight %}
добавьте
{% highlight ruby %}
has_many :comments
{% endhighlight %}

Модель Comment также должна знать о своей связи с Idea. Так что отройте app/models/comment.rb и после строки
{% highlight ruby %}
class Comment < ActiveRecord::Base
{% endhighlight %}

добавьте
{% highlight ruby %}
belongs_to :idea
{% endhighlight %}

## Шаг 5: Отображение формы комментирования и существующих комментариев

Откройте app/views/ideas/show.htmlи после image_tag
{% highlight erb %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

добавьте
{% highlight erb %}
<h3>Comments</h3>
<% @idea.comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br />
    <p><%= comment.body %><p>
  </div>
<% end %>
<%= render 'comments/form' %>
{% endhighlight %}

В app/controllers/ideas_controller.rb добавьте в show экшен после этой строки
In app/controllers/ideas_controller.rb add to show action after the row
{% highlight ruby %}
@idea = Idea.find(params[:id])
{% endhighlight %}

вот это
{% highlight ruby %}
@comment = @idea.comments.new
{% endhighlight %}

Откройте comments/_form.html и после
{% highlight erb %}
  <div class="field">
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </div>
{% endhighlight %}

добавьте строку
{% highlight erb %}
<%= f.hidden_field :idea_id %>
{% endhighlight %}
Вот и всё. Теперь просмотрите идею, которую вы создали, и вы увидите форму для добавления комментария.