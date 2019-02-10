# Migration

```shell
rake active_storage:install:migrations
rake db:migrate
```
This migration adds two tables - active_storage_blobs and active_storage_attachments. These are for the 2 models Blob and Attachment. Blob stores metadata like filename, content-type, byte size and checksum. The actual file is stored in the storage service or disk depending on your settings.

### Active Record Model

Let's say you want to attach one or multiple images to a Comment. First, we have to create the Comment model. We'll use the generator to create the resource.

```shell
rails g resource comment content:text
```

### One Attachment
You don't need to use the Blob and Attachment directly. To attach one image to a Comment, use has_one_attached

```shell
class Comment < ApplicationRecord
  has_one_attached :image
end
```

### One Attachment
You don't need to use the Blob and Attachment directly. To attach one image to a Comment, use has_one_attached

```shell
class Comment < ApplicationRecord
  has_one_attached :image
end
```

### Example Comment

```shell
class Comment < ApplicationRecord
  has_many_attached :image
end
```

```shell
class CommentController < ApplicationController
  def create
    comment = Comment.create! params.require(:comment).permit(:content)
    comment.images.attach(params[:comment][:images])
    redirect_to comment    
  end
end
```

```shell
# new.html.erb
<%= form_with model: @comment, local: true  do |form| %>
  <%= form.text_area :content %><br><br>
  <%= form.file_field :images, multiple: true %><br>
  <%= form.submit %>
<% end %>
```

### Many Attachments
We only need to modify a few things from the code above to use multiple attachments.

```shell
has_many_attached instead of has_one_attached
comment.images instead of comment.image
multiple: true on file_field to allow selection of multiple files
```

### Configuration
Active Storage is enabled by default when you create a new Rails application. config/active_storage.yml is created and config.active_storage.service is set to :local on both development.rb and production.rb.

local is set to use Disk and the storage directory is used.

local:
  service: Disk
  root: <%= Rails.root.join("storage") %>
To change this, you can specify config.active_storage.service to :amazon, :google, or :microsoft and set the appropriate values on config/storage.yml.

For Amazon S3, you can pass access_key_id, secret_access_key, region, and bucket. For Google Cloud Storage, you can pass project, keyfile, and bucket. For Microsoft Azure Storage, you can pass path, storage_account_name, storage_access_key, and container.

See the generated storage.yml for examples. The credentials should be added using bin/rails credentials:edit. Read our blog post about encrypted credentials on Rails 5.2 here.

You also need to add the necessary gem on the Gemfile for your chosen service. This can be aws-sdk-s3, google-cloud-storage, or azure-storage.
