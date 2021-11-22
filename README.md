# RailsでAPIを作成する

 Railsのapiモードを使って簡単なAPIを作成し、試してみましょう。

## 手順
1\.　APIモードでRailsアプリの作成

2\.　モデル・コントローラの作成

3\.　名前空間を意識したルーティングの設定

4\.　コントローラの設定

5\.　browser/postmanを使用して動作の確認

<br />

## APIモードでRailsアプリの作成
```
console
$ rails new blog --api
```
<br />

## モデル・コントローラの作成
今回はtitleというstringを持ったpostというテーブルを作成します。
> $ rails g model post title:string

> $ rails g controller posts

> $ rails db:create

> $ rake db:migrate

<br />

## ルーティングの設定
```
config/routes.rb

 Rails.application.routes.draw do
   resources :posts
 end
```
rails routesで確認すると以下の様になります。
```
posts GET    /posts(.:format)                                                                                  posts#index
      POST   /posts(.:format)                                                                                  posts#create
post GET    /posts/:id(.:format)                                                                              posts#show
     PATCH  /posts/:id(.:format)                                                                              posts#update
     PUT    /posts/:id(.:format)                                                                              posts#update
     DELETE /posts/:id(.:format)  
```
<br />

## コントローラの設定
コントローラの中身を外部からajaxリクエスト等で情報の作成・取得・削除・編集が可能になるよう設定します。
```
posts.controller.rb

class PostsController < ApplicationController
  before_action :set_post, only: %i[show update destroy]

  def index
    posts = Post.order(created_at: :desc)
    render json: { status: 'SUCCESS', message: 'Loaded posts', data: posts }
  end

  def show
    render json: { status: 'SUCCESS', message: 'Loaded the post', data: @post }
  end

  def create
    post = Post.new(post_params)
    if post.save
      render json: { status: 'SUCCESS', data: post }
    else
      render json: { status: 'ERROR', data: post.errors }
    end
  end

  def destroy
    @post.destroy
    render json: { status: 'SUCCESS', message: 'Deleted the post', data: @post }
  end

  def update
    if @post.update(post_params)
      render json: { status: 'SUCCESS', message: 'Updated the post', data: @post }
    else
      render json: { status: 'SUCCESS', message: 'Not updated', data: @post.errors }
    end
  end

  private

  def set_post
    @post = Post.find(params[:id])
  end

  def post_params
    params.require(:post).permit(:title)
  end
end
```
<br />

## browser/postmanを使用して動作の確認
まずrailsコンソールを利用していくつかデータを作成しましょう。
```
$ rails c 
2.4.4 :001 > Post.create(title:'title1')
2.4.4 :001 > Post.create(title:'title2')
```
まず作成したAPIを動かします。
```
$ rails s
```

<img width="526" alt="api" src="https://user-images.githubusercontent.com/83254076/142792835-f0baa310-e7a6-4560-b5a8-0a13793c63a8.png">

