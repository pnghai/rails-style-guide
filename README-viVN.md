# Mở đầu

> Role models are important. <br/>
> -- Officer Alex J. Murphy / RoboCop

Mục tiêu của tài liệu này là tập hợp các cài đặt thực tế tốt nhất (best practice) và các quy định phong cách (style prescription) khi phát triển Ruby on Rails 4. Tài liệu này nhằm bổ sung cho tài liệu huớng cộng đồng hiện có về [Hướng dẫn Phong cách lập trình Ruby](https://github.com/bbatsov/ruby-style-guide).

Một số lời khuyên duới đây chỉ phù hợp cho Rails 4.0+.

Bạn có thể xuất phiên bản PDF hoặc HTML của tâi liệu này bằng [Transmuter](https://github.com/TechnoGate/transmuter).

Các bản dịch của tài liệu này hiện có trong các ngôn ngữ sau:

* [Tiếng Anh](https://github.com/bbatsov/rails-style-guide)
* [Tiếng Hoa Giản thể](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhCN.md)
* [Tiếng Hoa Phồn thể](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhTW.md)
* [Tiếng Nga](https://github.com/arbox/rails-style-guide/blob/master/README-ruRU.md)
* [Thổ Nhĩ Kỳ](https://github.com/tolgaavci/rails-style-guide/blob/master/README-trTR.md)
* [Tiếng Nhật](https://github.com/satour/rails-style-guide/blob/master/README-jaJA.md)
* [Tiếng Việt](https://github.com/pnghai/rails-style-guide/blob/master/README-viVN.md)

# Hướng dẫn Phong cách Lập trình Rails

Hướng dẫn phong cách lập trình Rails này khuyến nghị các cài đặt thực tế tốt nhất để các lập trình viên Rails có thể viết mã sao cho nguời khác có thể bảo trì được. Hướng dẫn phong cách nhằm phản ánh cách sử dụng quen thuộc các cài đặt trong thực tế, và hướng dẫn phong cách như vậy hướng đến một lý tưởng bị từ chối bởi những người mà nó nhắm tới để tránh các rủi ro xảy ra vốn không quen dùng &ndash; bất kể hướng dẫn tốt đến đâu (?).

Tài liệu này được chia thành vài phần gồm các quy ước liên quan. Tác giả đã cố gắng thêm các giải thích đằng sau các quy định đó (nếu phần đó không có là vì nó quá hiển nhiên).

Tác giả không viết về các luật trên trời rơi xuống - hầu hết là dựa trên kinh nghiệm từ công việc của mình, phản hồi và đề xuất của các thành viên cộng đồng Rails và các nguồn tài nguyên lập trình Rails đáng trân trọng.

## Mục lục

* [Configuration - Cấu hình](#configuration)
* [Routing - Định tuyến](#routing)
* [Controller - Lớp điều khiển](#controllers)
* [Model - Lớp Mô hình](#models)
* [Migrations - Trộn nhập](#migrations)
* [Views - L trình bày](#views)
* [Internationalization- Quốc tế hóa](#internationalization)
* [Assets - Tài nguyên](#assets)
* [Mailers - Gửi mail](#mailers)
* [Bundler - Bộ đóng gói](#bundler)
* [Flawed Gems](#flawed-gems)
* [Managing processes - Quản lý tiến trình](#managing-processes)

## <a id="configuration"></a>Configuration - Cấu hình

* <a name="config-initializers"></a>
  Đặt các mã khởi tạo tùy biến của bạn vào `config/initializers`. Mã trong phần khởi tạo được thực thi khi ứng dụng khởi động.
<sup>[[link](#config-initializers)]</sup>

* <a name="gem-initializers"></a>
  Giữ mã khởi tạo của từng gem trong các file riêng biệt với tên giống như tên gem, ví dụ `carrierwave.rb`, `active_admin.rb`, v.v...
<sup>[[link](#gem-initializers)]</sup>

* <a name="dev-test-prod-configs"></a>
  Điều chỉnh các thiết lập cho các môi trường phát triển, kiểm thử, và triển khai (production) (trong các tập tin tương ứng ở thư mục `config/environments/`)
<sup>[[link](#dev-test-prod-configs)]</sup>

  * Đánh dấu các tài nguyên bổ sung để biên dịch sẵn (precompilation) (nếu có):

    ```Ruby
    # config/environments/production.rb
    # Biên dịch sẵn các tài nguyên phụ (application.js, application.css,
    #cùng tất cả các tập tin phi-JS/CSS được thêm vào)
    config.assets.precompile += %w( rails_admin/rails_admin.css rails_admin/rails_admin.js )
    ```

* <a name="app-config"></a>
  Lưu cấu hình áp dụng cho mọi môi trường vào tập tin `config/application.rb`.
<sup>[[link](#app-config)]</sup>

* <a name="staging-like-prod"></a>
  Tạo thêm môi trường `staging` gần giống với `production`.
<sup>[[link](#staging-like-prod)]</sup>

## Routing - Định tuyến ## {#routing}

* <a name="member-collection-routes"></a>
  Khi bạn cần thêm nhiều hành động cho một tài nguyên RESTful (mà bạn có thực sự cần không vậy?) sử dụng tuyến(route) `member` và `collection`.<sup>[[link](#member-collection-routes)]</sup>

    ```Ruby
    # xấu
    get 'subscriptions/:id/unsubscribe'
    resources :subscriptions
    
    # tốt
    resources :subscriptions do
    get 'unsubscribe', on: :member
    end
    
    # xấu
    get 'photos/search'
    resources :photos
    
    # tốt
    resources :photos do
        get 'search', on: :collection
    end
    ```

* <a name="many-member-collection-routes"></a>
  Nếu bạn cần định nghĩa nhiều tuyến `member/collection` cùng lúc, nên thay thế bằng cú pháp khối.
<sup>[[link](#many-member-collection-routes)]</sup>

```Ruby
resources :subscriptions do
member do
  get 'unsubscribe'
  # nhiều tuyến nữa
end
end

resources :photos do
collection do
  get 'search'
  # nhiều tuyến nữa
end
end
```

* <a name="nested-routes"></a>
  Sử dụng các tuyến lồng nhau(nested routes) để diễn tả quan hệ giữa các mô hình (model) ActiveRecord tốt hơn.
<sup>[[link](#nested-routes)]</sup>

```Ruby
class Post < ActiveRecord::Base
    has_many :comments
end

class Comments < ActiveRecord::Base
    belongs_to :post
end

# routes.rb
resources :posts do
    resources :comments
end
```

* <a name="namespaced-routes"></a>
  Dùng các tuyến theo tên dành riêng (namespaced route) để gom nhóm các hành động liên quan.
<sup>[[link](#namespaced-routes)]</sup>

  ```Ruby
  namespace :admin do
    # Điều hướng /admin/products/* đến Admin::ProductsController
    # (app/controllers/admin/products_controller.rb)
    resources :products
  end
  ```

* <a name="no-wild-routes"></a>
  Không bao giờ dùng legacy wild controller route. Tuyến này sẽ khiến mọi hành động trong từng bộ điều khiển đều truy cập được qua các GET request.
<sup>[[link](#no-wild-routes)]</sup>

  ```Ruby
  # kinh khủng khiếp
  match ':controller(/:action(/:id(.:format)))'
  ```

* <a name="no-match-routes"></a>
  Đừng dùng `match` để định nghĩa bất cứ tuyến nào trừ phi cần phải ánh xạ nhiều kiểu request cùng lúc trong số `[:get, :post, :patch, :put, :delete]` đến một hành vi đơn thuần sử dụng tùy chọn `:via`.
<sup>[[link](#no-match-routes)]</sup>

## Controllers - Lớp điều khiển ## {#controllers}

* <a name="skinny-controllers"></a>
  Giữ các lớp điều khiển thật gọn - chúng chỉ nên lấy dữ liệu cho tầng view và không nên chứa bất cứ logic nghiệp vụ nào (mọi logic nghiệp vụ nên nằm tự nhiên trong mô hình).
<sup>[[link](#skinny-controllers)]</sup>

* <a name="one-method"></a>
  Từng hành động (action) trong lớp điều khiển nên (về mặt lý tưởng) gọi chỉ một phương thức khác ngoài một phương thức khởi tạo : tìm hoặc tạo mới.
<sup>[[link](#one-method)]</sup>

* <a name="shared-instance-variables"></a>
  Chia sẻ không quá hơn 2 biến thực thể giữa một lớp điều khiển và một lớp trình bày.
<sup>[[link](#shared-instance-variables)]</sup>

## Models - Lớp mô hình ## {#models}

* <a name="model-classes"></a>
  Cứ thoải mái tạo các lớp mô hình phi-ActiveRecord.
<sup>[[link](#model-classes)]</sup>

* <a name="meaningful-model-names"></a>
  Đặt tên các lớp mô hình bằng các tên ngắn có ý nghĩa và không chứa các chữ viết tắt.
<sup>[[link](#meaningful-model-names)]</sup>

* <a name="activeattr-gem"></a>
  Nếu bạn cần các đối tượng thuộc lớp mô hình hỗ trợ hành vi của ActiveRecord (như là xác thực), nên sử dụng gem [ActiveAttr](https://github.com/cgriego/active_attr).
<sup>[[link](#activeattr-gem)]</sup>

  ```Ruby
  class Message
    include ActiveAttr::Model

    attribute :name
    attribute :email
    attribute :content
    attribute :priority

    attr_accessible :name, :email, :content

    validates :name, presence: true
    validates :email, format: { with: /\A[-a-z0-9_+\.]+\@([-a-z0-9]+\.)+[a-z0-9]{2,4}\z/i }
    validates :content, length: { maximum: 500 }
  end
  ```

  Xem ví dụ đầy đủ hơn tại 
  [RailsCast về đề tài này](http://railscasts.com/episodes/326-activeattr).

### ActiveRecord

* <a name="keep-ar-defaults"></a>
  Tránh thay đổi các mặc định của ActiveRecord (các tên bảng, khóa chính, v.v...) trừ phi bạn có một lý do rất tốt (như là một cơ sở dữ liệu mà bạn không có quyền kiểm soát).
<sup>[[link](#keep-ar-defaults)]</sup>

  ```Ruby
  # xấu - đừng làm điều dưới đây nếu bạn có thể thay đổi schema
  class Transaction < ActiveRecord::Base
    self.table_name = 'order'
    ...
  end
  ```

* <a name="macro-style-methods"></a>
  Nhóm các phương thức kiểu macro (`has_many`, `validates`, v.v...) vào đầu phần định nghĩa lớp đối tượng.
<sup>[[link](#macro-style-methods)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    # sử dụng tầm vực mặc định trước tiên (nếu có)
    default_scope { where(active: true) }

    # kế tiếp là khai báo hằng
    GENDERS = %w(male female)

    # sau đó là đặt các macro liên quan đến attr
    attr_accessor :formatted_date_of_birth

    attr_accessible :login, :first_name, :last_name, :email, :password

    # theo sau là các macros về các quan hệ nối kết
    belongs_to :country

    has_many :authentications, dependent: :destroy

    # rồi đến các macro xác thực
    validates :email, presence: true
    validates :username, presence: true
    validates :username, uniqueness: { case_sensitive: false }
    validates :username, format: { with: /\A[A-Za-z][A-Za-z0-9._-]{2,19}\z/ }
    validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true}

    # rồi khai báo các callback
    before_save :cook
    before_save :update_username_lower

    # các macro khác (như của devise) nên đặt sau các callback

    ...
  end
  ```

* <a name="has-many-through"></a>
  Dùng `has_many :through` thay cho `has_and_belongs_to_many`. Sử dụng `has_many :through` cho phép các thuộc tính và xác thực phụ hoạt động trên lớp mô hình đi cùng (join model).
<sup>[[link](#has-many-through)]</sup>

  ```Ruby
  # không hay lắm - dùng has_and_belongs_to_many
  class User < ActiveRecord::Base
    has_and_belongs_to_many :groups
  end

  class Group < ActiveRecord::Base
    has_and_belongs_to_many :users
  end

  # cách tốt hơn - dùng has_many :through
  class User < ActiveRecord::Base
    has_many :memberships
    has_many :groups, through: :memberships
  end

  class Membership < ActiveRecord::Base
    belongs_to :user
    belongs_to :group
  end

  class Group < ActiveRecord::Base
    has_many :memberships
    has_many :users, through: :memberships
  end
  ```

* <a name="read-attribute"></a>
  Dùng `self[:attribute]` thay cho `read_attribute(:attribute)`.
<sup>[[link](#read-attribute)]</sup>

  ```Ruby
  # xấu
  def amount
    read_attribute(:amount) * 100
  end

  # tốt
  def amount
    self[:amount] * 100
  end
  ```

* <a name="write-attribute"></a>
  Dùng `self[:attribute] = value` thay vì `write_attribute(:attribute, value)`.
<sup>[[link](#write-attribute)]</sup>

  ```Ruby
  # xấu
  def amount
    write_attribute(:amount, 100)
  end

  # tốt
  def amount
    self[:amount] = 100
  end
  ```

* <a name="sexy-validations"></a>
  Luôn dùng các [xác thực "sexy"](http://thelucid.com/2010/01/08/sexy-validation-in-edge-rails-rails-3/) mới.
<sup>[[link](#sexy-validations)]</sup>

  ```Ruby
  # xấu
  validates_presence_of :email

  # tốt
  validates :email, presence: true
  ```

* <a name="custom-validator-file"></a>
  Khi dùng nhiều lần xác thực tùy biến hay một xác thực là ánh xạ biểu thức hồi quy nào đó, hãy tạo một tập tin bộ xác thực tùy biến (custom validator file).
<sup>[[link](#custom-validator-file)]</sup>

  ```Ruby
  # xấu
  class Person
    validates :email, format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i }
  end

  # tốt
  class EmailValidator < ActiveModel::EachValidator
    def validate_each(record, attribute, value)
      record.errors[attribute] << (options[:message] || 'is not a valid email') unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
    end
  end

  class Person
    validates :email, email: true
  end
  ```

* <a name="app-validators"></a>
  Giữ các bộ xác thực tùy biến trong thư mục `app/validators`.
<sup>[[link](#app-validators)]</sup>

* <a name="custom-validators-gem"></a>
  Cân nhắc trích xuất các bộ xác thực tùy biến ra một gem dùng chung nếu bạn đang bảo trì vài ứng dụng liên quan hoặc là các bộ xác thực ấy đủ chung.
<sup>[[link](#custom-validators-gem)]</sup>

* <a name="named-scopes"></a>
  Thoải mái dùng các tầm vực đặt tên riêng.
<sup>[[link](#named-scopes)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    scope :active, -> { where(active: true) }
    scope :inactive, -> { where(active: false) }

    scope :with_orders, -> { joins(:orders).select('distinct(users.id)') }
  end
  ```

* <a name="named-scope-class"></a>
  Khi tạo một tầm vực có tên riêng được định nghĩa với một lambda và các tham số trở nên quá phức tạp, thay vào đó ta nên tạo một phương thức thuộc về lớp đối tượng phục vụ cùng mục đích như tầm vực đã nói, và trả về một đối tượng `ActiveRecord::Relation`. Có thể cho rằng bạn định nghĩa một tầm vực đơn giản hơn như sau.
<sup>[[link](#named-scope-class)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    def self.with_orders
      joins(:orders).select('distinct(users.id)')
    end
  end
  ```

* <a name="beware-update-attribute"></a>
  Coi chừng hành vi của 
  [`update_attribute`](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update_attribute)
  method. It doesn't run the model validations (unlike `update_attributes`) and
  could easily corrupt the model state.
<sup>[[link](#beware-update-attribute)]</sup>

* <a name="user-friendly-urls"></a>
  Use user-friendly URLs. Show some descriptive attribute of the model in the URL
  rather than its `id`.  There is more than one way to achieve this:
<sup>[[link](#user-friendly-urls)]</sup>

  * Override the `to_param` method of the model. This method is used by Rails
    for constructing a URL to the object.  The default implementation returns
    the `id` of the record as a String.  It could be overridden to include another
    human-readable attribute.

      ```Ruby
      class Person
        def to_param
          "#{id} #{name}".parameterize
        end
      end
      ```

  In order to convert this to a URL-friendly value, `parameterize` should be
  called on the string. The `id` of the object needs to be at the beginning so
  that it can be found by the `find` method of ActiveRecord.

  * Use the `friendly_id` gem. It allows creation of human-readable URLs by
    using some descriptive attribute of the model instead of its `id`.

      ```Ruby
      class Person
        extend FriendlyId
        friendly_id :name, use: :slugged
      end
      ```

  Check the [gem documentation](https://github.com/norman/friendly_id) for more
  information about its usage.

* <a name="find-each"></a>
  Use `find_each` to iterate over a collection of AR objects. Looping through a
  collection of records from the database (using the `all` method, for example)
  is very inefficient since it will try to instantiate all the objects at once.
  In that case, batch processing methods allow you to work with the records in
  batches, thereby greatly reducing memory consumption.
<sup>[[link](#find-each)]</sup>


  ```Ruby
  # xấu
  Person.all.each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').each do |person|
    person.party_all_night!
  end

  # tốt
  Person.find_each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').find_each do |person|
    person.party_all_night!
  end
  ```

* <a name="before_destroy"></a>
  Since [Rails creates callbacks for dependent
  associations](https://github.com/rails/rails/issues/3458), always call
  `before_destroy` callbacks that perform validation with `prepend: true`.

  ```Ruby
  # xấu (roles will be deleted automatically even if super_admin? is true)
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end

  # tốt
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable, prepend: true

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end
  ```


## Migrations

* <a name="schema-version"></a>
  Keep the `schema.rb` (or `structure.sql`) under version control.
<sup>[[link](#schema-version)]</sup>

* <a name="db-schema-load"></a>
  Use `rake db:schema:load` instead of `rake db:migrate` to initialize an empty
  database.
<sup>[[link](#db-schema-load)]</sup>

* <a name="default-migration-values"></a>
  Enforce default values in the migrations themselves instead of in the
  application layer.
<sup>[[link](#default-migration-values)]</sup>

  ```Ruby
  # xấu - application enforced default value
  def amount
    self[:amount] or 0
  end
  ```

  While enforcing table defaults only in Rails is suggested by many
  Rails developers, it's an extremely brittle approach that
  leaves your data vulnerable to many application bugs.  And you'll
  have to consider the fact that most non-trivial apps share a
  database with other applications, so imposing data integrity from
  the Rails app is impossible.

* <a name="foreign-key-constraints"></a>
  Enforce foreign-key constraints. While ActiveRecord does not support them
  natively, there some great third-party gems like
  [schema_plus](https://github.com/lomba/schema_plus) and
  [foreigner](https://github.com/matthuhiggins/foreigner).
<sup>[[link](#foreign-key-constraints)]</sup>

* <a name="change-vs-up-down"></a>
  When writing constructive migrations (adding tables or columns),
  use the `change` method instead of `up` and `down` methods.
<sup>[[link](#change-vs-up-down)]</sup>

  ```Ruby
  # the old way
  class AddNameToPeople < ActiveRecord::Migration
    def up
      add_column :people, :name, :string
    end

    def down
      remove_column :people, :name
    end
  end

  # the new prefered way
  class AddNameToPeople < ActiveRecord::Migration
    def change
      add_column :people, :name, :string
    end
  end
  ```

* <a name="no-model-class-migrations"></a>
  Don't use model classes in migrations. The model classes are constantly
  evolving and at some point in the future migrations that used to work might
  stop, because of changes in the models used.
<sup>[[link](#no-model-class-migrations)]</sup>

## Views

* <a name="no-direct-model-view"></a>
  Never call the model layer directly from a view.
<sup>[[link](#no-direct-model-view)]</sup>

* <a name="no-complex-view-formatting"></a>
  Never make complex formatting in the views, export the formatting to a method
  in the view helper or the model.
<sup>[[link](#no-complex-view-formatting)]</sup>

* <a name="partials"></a>
  Mitigate code duplication by using partial templates and layouts.
<sup>[[link](#partials)]</sup>

## Internationalization

* <a name="locale-texts"></a>
  No strings or other locale specific settings should be used in the views,
  models and controllers. These texts should be moved to the locale files in the
  `config/locales` directory.
<sup>[[link](#locale-texts)]</sup>

* <a name="translated-labels"></a>
  When the labels of an ActiveRecord model need to be translated, use the
  `activerecord` scope:
<sup>[[link](#translated-labels)]</sup>

  ```
  en:
    activerecord:
      models:
        user: Member
      attributes:
        user:
          name: 'Full name'
  ```

  Then `User.model_name.human` will return "Member" and
  `User.human_attribute_name("name")` will return "Full name". These
  translations of the attributes will be used as labels in the views.


* <a name="organize-locale-files"></a>
  Separate the texts used in the views from translations of ActiveRecord
  attributes. Place the locale files for the models in a folder `models` and the
  texts used in the views in folder `views`.
<sup>[[link](#organize-locale-files)]</sup>

  * When organization of the locale files is done with additional directories,
    these directories must be described in the `application.rb` file in order
    to be loaded.

      ```Ruby
      # config/application.rb
      config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}')]
      ```

* <a name="shared-localization"></a>
  Place the shared localization options, such as date or currency formats, in
  files under the root of the `locales` directory.
<sup>[[link](#shared-localization)]</sup>

* <a name="short-i18n"></a>
  Use the short form of the I18n methods: `I18n.t` instead of `I18n.translate`
  and `I18n.l` instead of `I18n.localize`.
<sup>[[link](#short-i18n)]</sup>

* <a name="lazy-lookup"></a>
  Use "lazy" lookup for the texts used in views. Let's say we have the following
  structure:
<sup>[[link](#lazy-lookup)]</sup>

  ```
  en:
    users:
      show:
        title: 'User details page'
  ```

  The value for `users.show.title` can be looked up in the template
  `app/views/users/show.html.haml` like this:

  ```Ruby
  = t '.title'
  ```

* <a name="dot-separated-keys"></a>
  Use the dot-separated keys in the controllers and models instead of specifying
  the `:scope` option. The dot-separated call is easier to read and trace the
  hierarchy.
<sup>[[link](#dot-separated-keys)]</sup>

  ```Ruby
  # use this call
  I18n.t 'activerecord.errors.messages.record_invalid'

  # instead of this
  I18n.t :record_invalid, :scope => [:activerecord, :errors, :messages]
  ```

* <a name="i18n-guides"></a>
  More detailed information about the Rails i18n can be found in the [Rails
  Guides](http://guides.rubyonrails.org/i18n.html)
<sup>[[link](#i18n-guides)]</sup>

## Assets

Use the [assets pipeline](http://guides.rubyonrails.org/asset_pipeline.html) to leverage organization within
your application.

* <a name="reserve-app-assets"></a>
  Reserve `app/assets` for custom stylesheets, javascripts, or images.
<sup>[[link](#reserve-app-assets)]</sup>

* <a name="lib-assets"></a>
  Use `lib/assets` for your own libraries, that doesn’t really fit into the
  scope of the application.
<sup>[[link](#lib-assets)]</sup>

* <a name="vendor-assets"></a>
  Third party code such as [jQuery](http://jquery.com/) or
  [bootstrap](http://twitter.github.com/bootstrap/) should be placed in
  `vendor/assets`.
<sup>[[link](#vendor-assets)]</sup>

* <a name="gem-assets"></a>
  When possible, use gemified versions of assets (e.g.
  [jquery-rails](https://github.com/rails/jquery-rails),
  [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails),
  [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass),
  [zurb-foundation](https://github.com/zurb/foundation)).
<sup>[[link](#gem-assets)]</sup>

## Mailers

* <a name="mailer-name"></a>
  Name the mailers `SomethingMailer`. Without the Mailer suffix it isn't
  immediately apparent what's a mailer and which views are related to the
  mailer.
<sup>[[link](#mailer-name)]</sup>

* <a name="html-plain-email"></a>
  Provide both HTML and plain-text view templates.
<sup>[[link](#html-plain-email)]</sup>

* <a name="enable-delivery-errors"></a>
  Enable errors raised on failed mail delivery in your development environment.
  The errors are disabled by default.
<sup>[[link](#enable-delivery-errors)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.raise_delivery_errors = true
  ```

* <a name="local-smtp"></a>
  Use a local SMTP server like
  [Mailcatcher](https://github.com/sj26/mailcatcher) in the development
  environment.
<sup>[[link](#local-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.smtp_settings = {
    address: 'localhost',
    port: 1025,
    # more settings
  }
  ```

* <a name="default-hostname"></a>
  Provide default settings for the host name.
<sup>[[link](#default-hostname)]</sup>

  ```Ruby
  # config/environments/development.rb
  config.action_mailer.default_url_options = { host: "#{local_ip}:3000" }

  # config/environments/production.rb
  config.action_mailer.default_url_options = { host: 'your_site.com' }

  # in your mailer class
  default_url_options[:host] = 'your_site.com'
  ```

* <a name="url-not-path-in-email"></a>
  If you need to use a link to your site in an email, always use the `_url`, not
  `_path` methods. The `_url` methods include the host name and the `_path`
  methods don't.
<sup>[[link](#url-not-path-in-email)]</sup>

  ```Ruby
  # xấu
  You can always find more info about this course
  = link_to 'here', course_path(@course)

  # tốt
  You can always find more info about this course
  = link_to 'here', course_url(@course)
  ```

* <a name="email-addresses"></a>
  Format the from and to addresses properly. Use the following format:
<sup>[[link](#email-addresses)]</sup>

  ```Ruby
  # in your mailer class
  default from: 'Your Name <info@your_site.com>'
  ```

* <a name="delivery-method-test"></a>
  Make sure that the e-mail delivery method for your test environment is set to
  `test`:
<sup>[[link](#delivery-method-test)]</sup>

  ```Ruby
  # config/environments/test.rb

  config.action_mailer.delivery_method = :test
  ```

* <a name="delivery-method-smtp"></a>
  The delivery method for development and production should be `smtp`:
<sup>[[link](#delivery-method-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb, config/environments/production.rb

  config.action_mailer.delivery_method = :smtp
  ```

* <a name="inline-email-styles"></a>
  When sending html emails all styles should be inline, as some mail clients
  have problems with external styles. This however makes them harder to maintain
  and leads to code duplication. There are two similar gems that transform the
  styles and put them in the corresponding html tags:
  [premailer-rails](https://github.com/fphilipe/premailer-rails) and
  [roadie](https://github.com/Mange/roadie).
<sup>[[link](#inline-email-styles)]</sup>

* <a name="background-email"></a>
  Sending emails while generating page response should be avoided. It causes
  delays in loading of the page and request can timeout if multiple email are
  sent. To overcome this emails can be sent in background process with the help
  of [sidekiq](https://github.com/mperham/sidekiq) gem.
<sup>[[link](#background-email)]</sup>

## Bundler

* <a name="dev-test-gems"></a>
  Put gems used only for development or testing in the appropriate group in the
  Gemfile.
<sup>[[link](#dev-test-gems)]</sup>

* <a name="only-good-gems"></a>
  Use only established gems in your projects. If you're contemplating on
  including some little-known gem you should do a careful review of its source
  code first.
<sup>[[link](#only-good-gems)]</sup>

* <a name="os-specific-gemfile-locks"></a>
  OS-specific gems will by default result in a constantly changing
  `Gemfile.lock` for projects with multiple developers using different operating
  systems.  Add all OS X specific gems to a `darwin` group in the Gemfile, and
  all Linux specific gems to a `linux` group:
<sup>[[link](#os-specific-gemfile-locks)]</sup>

  ```Ruby
  # Gemfile
  group :darwin do
    gem 'rb-fsevent'
    gem 'growl'
  end

  group :linux do
    gem 'rb-inotify'
  end
  ```

  To require the appropriate gems in the right environment, add the
  following to `config/application.rb`:

  ```Ruby
  platform = RUBY_PLATFORM.match(/(linux|darwin)/)[0].to_sym
  Bundler.require(platform)
  ```

* <a name="gemfile-lock"></a>
  Do not remove the `Gemfile.lock` from version control. This is not some
  randomly generated file - it makes sure that all of your team members get the
  same gem versions when they do a `bundle install`.
<sup>[[link](#gemfile-lock)]</sup>

## Flawed Gems

This is a list of gems that are either problematic or superseded by
other gems. You should avoid using them in your projects.

* [rmagick](http://rmagick.rubyforge.org/) - this gem is notorious for its
  memory consumption. Use
  [minimagick](https://github.com/probablycorey/mini_magick) instead.

* [autotest](http://www.zenspider.com/ZSS/Products/ZenTest/) - old solution for
  running tests automatically. Far inferior to
  [guard](https://github.com/guard/guard) and
  [watchr](https://github.com/mynyml/watchr).

* [rcov](https://github.com/relevance/rcov) - code coverage tool, not compatible
  with Ruby 1.9. Use [SimpleCov](https://github.com/colszowka/simplecov)
  instead.

* [therubyracer](https://github.com/cowboyd/therubyracer) - the use of this gem
  in production is strongly discouraged as it uses a very large amount of
  memory. I'd suggest using `node.js` instead.

This list is also a work in progress. Please, let me know if you know other
popular, but flawed gems.

## Managing processes

* <a name="foreman"></a>
  If your projects depends on various external processes use
  [foreman](https://github.com/ddollar/foreman) to manage them.
<sup>[[link](#foreman)]</sup>

# Further Reading

There are a few excellent resources on Rails style, that you should consider if
you have time to spare:

* [The Rails 4 Way](http://www.amazon.com/The-Rails-Addison-Wesley-Professional-Ruby/dp/0321944275)
* [Ruby on Rails Guides](http://guides.rubyonrails.org/)
* [The RSpec Book](http://pragprog.com/book/achbd/the-rspec-book)
* [The Cucumber Book](http://pragprog.com/book/hwcuc/the-cucumber-book)
* [Everyday Rails Testing with RSpec](https://leanpub.com/everydayrailsrspec)
* [Better Specs for RSpec](http://betterspecs.org)

# Contributing

Nothing written in this guide is set in stone. It's my desire to work together
with everyone interested in Rails coding style, so that we could ultimately
create a resource that will be beneficial to the entire Ruby community.

Feel free to open tickets or send pull requests with improvements. Thanks in
advance for your help!

You can also support the project (and RuboCop) with financial contributions via
[gittip](https://www.gittip.com/bbatsov).

[![Support via Gittip](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

## Làm sao để đóng góp?

It's easy, just follow the [contribution guidelines](https://github.com/bbatsov/rails-style-guide/blob/master/CONTRIBUTING.md).

# Giấy phép

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a [Creative Commons Attribution 3.0 Unported
License](http://creativecommons.org/licenses/by/3.0/deed.en_US)

# Spread the Word

A community-driven style guide is of little use to a community that doesn't know
about its existence. Tweet about the guide, share it with your friends and
colleagues. Every comment, suggestion or opinion we get makes the guide just a
little bit better. And we want to have the best possible guide, don't we?

Thân ái,<br/>
[Bozhidar](https://twitter.com/bbatsov)
