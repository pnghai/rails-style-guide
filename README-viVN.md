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

* [Configuration - Cấu hình](#configuration---c%E1%BA%A5u-h%C3%ACnh)
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

## Configuration - Cấu hình

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
  Khi bạn cần thêm nhiều hành động cho một tài nguyên RESTful (mà bạn có thực sự cần không vậy?) sử dụng tuyến(route) `member` và `collection`.
<sup>[[link](#member-collection-routes)]</sup>

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
  Coi chừng hành vi của phương thức 
  [`update_attribute`](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update_attribute)
  . Phương thức này không gọi các xác thực của lớp mô hình (không như `update_attributes`) và có thể dễ dàng làm hỏng hiện trạng của lớp mô hình (model state).
<sup>[[link](#beware-update-attribute)]</sup>

* <a name="user-friendly-urls"></a>
  Dùng các URL thân thiện người dùng. Hiện một số thuộc tính mô tả của lớp mô hình trong URL thay vì `id` của nó. Có nhiều cách để thực hiện:
<sup>[[link](#user-friendly-urls)]</sup>

  * Nạp chồng phương thức `to_param` của lớp mô hình. Rails dùng phương thức này để dựng URL đến đối tượng thuộc về. Cài đặt mặc định trả về `id` của record như một biến kiểu String (chuỗi). Ta có thể nạo chồng nó để trả về thêm thuộc tính khác mà con người đọc được.

      ```Ruby
      class Person
        def to_param
          "#{id} #{name}".parameterize
        end
      end
      ```

  Để chuyển dữ liệu trả về thành giá trị phù hợp cho URL , `parameterize` nên được gọi trên chuỗi. Phần `id` của đối tượng đó cần đặt ở đầu để dễ dàng được tìm thấy bởi phương thức `find` của ActiveRecord.

  * Dùng gem `friendly_id`. Gem này cho phép tạo các URL con người đọc được bằng cách dùng một thuộc tính mô tả nào đó của lớp mô hình thay vì `id` của nó.

      ```Ruby
      class Person
        extend FriendlyId
        friendly_id :name, use: :slugged
      end
      ```

  Xem [tài liệu về gem](https://github.com/norman/friendly_id) để biết thêm chi tiết về cách dùng.

* <a name="find-each"></a>
  Dùng `find_each` để duyệt mảng (collection) các đối tượng thuộc lớp AR. Lặp qua mảng các record từ cơ sở dữ liệu (ví dụ, dùng phương thức `all`) vô cùng kém hiệu quả vì nó sẽ cố gắng khởi tạo tất cả đối tượng chung một lần.
  Trong trường hợp đó, các phương thức xử lý bó (batch processing) cho phép bạn làm việc với các record thành từng bó, dó đó mà giảm thiểu tối đa tiêu thụ bộ nhớ.
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
  Vì [Rails tạo ra các callback dành cho các quan hệ kết tập phụ thuộc](https://github.com/rails/rails/issues/3458), luôn luôn gọi các callback
  `before_destroy` thực hiện xác thực với `prepend: true`.

  ```Ruby
  # xấu (các role sẽ tự động bị xóa thậm chí nếu super_admin? trả về true)
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


## Migrations - Trộn nhập

* <a name="schema-version"></a>
  Giữ `schema.rb` (hoặc `structure.sql`) trong version control (kiểm soát phiên bản).
<sup>[[link](#schema-version)]</sup>

* <a name="db-schema-load"></a>
  Dùng `rake db:schema:load` thay cho `rake db:migrate` để khởi tạo cơ sở dữ liệu trống.
<sup>[[link](#db-schema-load)]</sup>

* <a name="default-migration-values"></a>
  Enforce các giá trị mặc định trong các migration của chúng thay vì trong tầng ứng dụng.
<sup>[[link](#default-migration-values)]</sup>

  ```Ruby
  # xấu - ứng dụng enforced giá trị mặc định
  def amount
    self[:amount] or 0
  end
  ```

  While enforcing table defaults only in Rails is suggested by many
  Rails developers, it's an extremely brittle approach that
  leaves your data vulnerable to many application bugs. Và bạn sẽ phải cân nhắc thực tế rằng hầu hết các ứng dụng non-trivial chia sẻ một cơ sở dữ liệu với các ứng dụng khác, do vậy mà imposing toàn vẹn dữ liệu từ ứng dụng Rails là bất khả thi.

* <a name="foreign-key-constraints"></a>
  Enforce các ràng buộc khóa ngoại. Dù ActiveRecord không hỗ trợ chúng natively, có sẵn một số gem bên thứ ba  tuyệt vời như
  [schema_plus](https://github.com/lomba/schema_plus) và
  [foreigner](https://github.com/matthuhiggins/foreigner).
<sup>[[link](#foreign-key-constraints)]</sup>

* <a name="change-vs-up-down"></a>
  Khi viết các constructive migration (thêm bảng hay cột),
  dùng phương thức `change` thay vì cặp phương thức `up` và `down`.
<sup>[[link](#change-vs-up-down)]</sup>

  ```Ruby
  # kiểu cũ
  class AddNameToPeople < ActiveRecord::Migration
    def up
      add_column :people, :name, :string
    end

    def down
      remove_column :people, :name
    end
  end

  # kiểu mới ngon hơn
  class AddNameToPeople < ActiveRecord::Migration
    def change
      add_column :people, :name, :string
    end
  end
  ```

* <a name="no-model-class-migrations"></a>
  Đừng dùng các lớp mô hình trong các migrations. Các lớp mô hình constantly evolving và một lúc nào đó trong tương lai, các migrations đang làm việc ổn có thể bị dừng, do các thay đổi trong các mô hình được dùng đến.
<sup>[[link](#no-model-class-migrations)]</sup>

## Views - Lớp trình bày

* <a name="no-direct-model-view"></a>
  Không bao giờ gọi trực tiếp tầng mô hình từ một lớp trình bày.
<sup>[[link](#no-direct-model-view)]</sup>

* <a name="no-complex-view-formatting"></a>
  Không bao giờ tạo các định dạng phức tạp trong các lớp trình bày, xuất phần định dạng thành một phương thức trong helper của lớp trình bày hoặc trong lớp mô hình.
<sup>[[link](#no-complex-view-formatting)]</sup>

* <a name="partials"></a>
  Mitigate trùng lặp mã bằng cách dùng các template và layout partial.
<sup>[[link](#partials)]</sup>

## Internationalization - Quốc tế hóa

* <a name="locale-texts"></a>
  Không dùng bất cứ chuỗi hay các thiết lập được bản địa hóa trong các view,
  model, hay controller nào. Những văn bản này nên được chuyển vào các tập tin bản địa hóa trong thư mục
  `config/locales`.
<sup>[[link](#locale-texts)]</sup>

* <a name="translated-labels"></a>
  Khi cần dịch các nhãn của một lớp mô hình kế thừa từ ActiveRecord, dùng tầm vực `activerecord`:
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

  Khi đó `User.model_name.human` sẽ trả về "Member" và
  `User.human_attribute_name("name")` sẽ trả về "Full name". Phần dịch thuật cho các thuộc tính này sẽ được dùng như các nhãn trong các view.


* <a name="organize-locale-files"></a>
  Tách các văn bản dùng trong các view khỏi phần dịch các thuộc tính của ActiveRecord. Đặt các tập tin bản địa hóa cho các lớp mô hình trong thư mục `models` và các văn bản dùng trong các view trong thư mục `views`.
<sup>[[link](#organize-locale-files)]</sup>

  * Khi làm xong việc tổ chức các tập tin bản địa hóa thành các thư mục phụ, các thư mục này nên được khai báo trong tập tin `application.rb` để chương trình nạp chúng.

      ```Ruby
      # config/application.rb
      config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}')]
      ```

* <a name="shared-localization"></a>
  Đặt các tùy chọn shared localization, như là thời gian hay định dạng tiền tệ, trong các tập tin nằm ngay gốc thư mục `locales`.
<sup>[[link](#shared-localization)]</sup>

* <a name="short-i18n"></a>
  Dùng dạng thu gọn của các phương thức thuộc lớp đối tượng I18n : `I18n.t` thay cho `I18n.translate`
  và `I18n.l` thay cho `I18n.localize`.
<sup>[[link](#short-i18n)]</sup>

* <a name="lazy-lookup"></a>
  Dùng "lazy" tra cứu các văn bản dùng trong các view. Let's say we have the following
  structure:
<sup>[[link](#lazy-lookup)]</sup>

  ```
  en:
    users:
      show:
        title: 'User details page'
  ```

  Giá trị cho `users.show.title` có thể được tra cứu trong template
  `app/views/users/show.html.haml` như sau:

  ```Ruby
  = t '.title'
  ```

* <a name="dot-separated-keys"></a>
  Dùng các khóa phân cắt bằng dấu chấm trong các controller và model thay vì khai báo tùy chọn `:scope` . Các lời gọi phân cắt bởi dấu chấm dễ đọc và truy nguyên phân cấp hơn.
<sup>[[link](#dot-separated-keys)]</sup>

  ```Ruby
  # dùng lời gọi này
  I18n.t 'activerecord.errors.messages.record_invalid'

  # thay vì thế này
  I18n.t :record_invalid, :scope => [:activerecord, :errors, :messages]
  ```

* <a name="i18n-guides"></a>
  Tìm đọc chi tiết hơn về Rails i18n trong [Hướng dẫn Rails](http://guides.rubyonrails.org/i18n.html)
<sup>[[link](#i18n-guides)]</sup>

## Assets - Tài nguyên phụ

Dùng [assets pipeline](http://guides.rubyonrails.org/asset_pipeline.html) để leverage tổ chức bên trong ứng dụng của bạn.

* <a name="reserve-app-assets"></a>
  Dành `app/assets` cho các stylesheet, javascript, hoặc hình ảnh tùy biến.
<sup>[[link](#reserve-app-assets)]</sup>

* <a name="lib-assets"></a>
  Dùng `lib/assets` cho các thư viện của riêng bạn, không phù hợp với việc nằm trong tầm ứng dụng.
<sup>[[link](#lib-assets)]</sup>

* <a name="vendor-assets"></a>
  Mã của bên thứ ba như [jQuery](http://jquery.com/) hay
  [bootstrap](http://twitter.github.com/bootstrap/) nên được đặt trong
  `vendor/assets`.
<sup>[[link](#vendor-assets)]</sup>

* <a name="gem-assets"></a>
  Cứ khi nào có thể, nên dùng bản gem hóa của các tài nguyên phụ (lấy ví dụ:
  [jquery-rails](https://github.com/rails/jquery-rails),
  [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails),
  [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass),
  [zurb-foundation](https://github.com/zurb/foundation)).
<sup>[[link](#gem-assets)]</sup>

## Mailers - Bộ gửi mail

* <a name="mailer-name"></a>
  Đặt tên các bộ gửi mail `SomethingMailer`. Không có đuôi Mailer it isn't
  immediately apparent what's a mailer và view nào liên quan tới mailer đó.
<sup>[[link](#mailer-name)]</sup>

* <a name="html-plain-email"></a>
  Tạo cả 2 phiên bản HTML và văn bản thô cho view template.
<sup>[[link](#html-plain-email)]</sup>

* <a name="enable-delivery-errors"></a>
  Bật báo lỗi khi gửi mail không được trong môi trường phát triển (dev env).
  Các lỗi này mặc định bị tắt.
<sup>[[link](#enable-delivery-errors)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.raise_delivery_errors = true
  ```

* <a name="local-smtp"></a>
  Dùng một server SMTP local như
  [Mailcatcher](https://github.com/sj26/mailcatcher) trong môi trường dev.
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
  Khai báo các thiết lập mặc định cho tên host.
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
  Nếu bạn cần dùng một liên kết đến website của bạn trong một email, luôn dùng `_url`, không phải phương thức `_path`. Phương thức `_url` chứa tên host còn phương thức`_path` thì không.
<sup>[[link](#url-not-path-in-email)]</sup>

  ```Ruby
  # xấu
  Bạn luôn có thể tìm thêm thông tin về khóa học này
  = link_to 'here', course_path(@course)

  # tốt
  Bạn luôn có thể tìm thêm thông tin về khóa học này
  = link_to 'here', course_url(@course)
  ```

* <a name="email-addresses"></a>
  Định dạng đúng phần địa chỉ gửi và nhận. Dùng định dạng sau đây:
<sup>[[link](#email-addresses)]</sup>

  ```Ruby
  # in your mailer class
  default from: 'Your Name <info@your_site.com>'
  ```

* <a name="delivery-method-test"></a>
  Đảm bảo rằng phương thức gửi e-mail trong môi trường kiểm thử của bạn được thiết lập là `test`:
<sup>[[link](#delivery-method-test)]</sup>

  ```Ruby
  # config/environments/test.rb

  config.action_mailer.delivery_method = :test
  ```

* <a name="delivery-method-smtp"></a>
  Phương thức gửi trong môi trường phát triển và sử dụng (development and production) nên là `smtp`:
<sup>[[link](#delivery-method-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb, config/environments/production.rb

  config.action_mailer.delivery_method = :smtp
  ```

* <a name="inline-email-styles"></a>
  Khi gửi các email html, mọi style nên là inline, vì một số trình đọc mail có vấn đề với các external style. Tuy vậy, điều này làm cho code trở nên khó bảo trì hơn và dẫn tới trùng mã. Có 2 gem tương tự chuyển các style và đặt chúng vào các thẻ html tương ứng là:
  [premailer-rails](https://github.com/fphilipe/premailer-rails) và
  [roadie](https://github.com/Mange/roadie).
<sup>[[link](#inline-email-styles)]</sup>

* <a name="background-email"></a>
  Cần tránh gửi các email trong khi tạo các trang phản hồi. It causes
  delays in loading of the page and request có thể bị timeout nếu nhiều mail được gửi đi cùng lúc. Để tránh điều này, các email có thể được gửi bằng các tiến trình ngầm với sự trợ giúp của gem [sidekiq](https://github.com/mperham/sidekiq).
<sup>[[link](#background-email)]</sup>

## Bundler - Bộ đóng gói

* <a name="dev-test-gems"></a>
  Đặt các gem chỉ dùng trong môi trường phát triển hay kiểm thử thành các nhóm tương ứng trong Gemfile.
<sup>[[link](#dev-test-gems)]</sup>

* <a name="only-good-gems"></a>
  Chỉ dùng các gem established trong các dự án của bạn. If you're contemplating on
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

  Để yêu cầu các gem tương ứng với đúng môi trường lập trình, thêm vào tập tin `config/application.rb` như sau:

  ```Ruby
  platform = RUBY_PLATFORM.match(/(linux|darwin)/)[0].to_sym
  Bundler.require(platform)
  ```

* <a name="gemfile-lock"></a>
  Không loại bỏ `Gemfile.lock` khỏi kiểm soát phiên bản (version control). Đấy không phải là file được tạo ngẫu nhiên - nó đảm bảo rằng tất cả thành viên trong đội của bạn có cùng các phiên bản gem khi thực hiện `bundle install`.
<sup>[[link](#gemfile-lock)]</sup>

## Flawed Gems

Sau đây là danh sách các gem hoặc là problematic hoặc superseded bởi các gem khác. Bạn nên tránh dùng chúng trong các dự án của mình.

* [rmagick](http://rmagick.rubyforge.org/) - gem này is notorious vì tốn bộ nhớ quá nhiều. Thay vào đó dùng
  [minimagick](https://github.com/probablycorey/mini_magick).

* [autotest](http://www.zenspider.com/ZSS/Products/ZenTest/) - giải pháp cũ để chạy test tự động. Far inferior to
  [guard](https://github.com/guard/guard) và
  [watchr](https://github.com/mynyml/watchr).

* [rcov](https://github.com/relevance/rcov) - code coverage tool, không tương thích với Ruby 1.9. Thay vào đó dùng [SimpleCov](https://github.com/colszowka/simplecov).

* [therubyracer](https://github.com/cowboyd/therubyracer) - Dùng gem này trong môi trường vận hành thực tế strongly discouraged vì nó ăn tài nguyên khủng khiếp. Tác giả đề xuất dùng `node.js` thay thế.

Danh sách này còn đang tiếp tục. Xin báo với tác giả nếu bạn biết những gem nổi tiếng, nhưng flawed khác.

## Managing processes - Quản lý tiến trình

* <a name="foreman"></a>
  Nếu các dự án của bạn phụ thuộc vào các chương trình bên ngoài khác, dùng 
  [foreman](https://github.com/ddollar/foreman) để quản lý chúng.
<sup>[[link](#foreman)]</sup>

# Further Reading - Đọc thêm

Có một số ích tài nguyên xuất sắc về phong cách lập trình Rails mà bạn nên đọc nếu có thời gian:

* [The Rails 4 Way](http://www.amazon.com/The-Rails-Addison-Wesley-Professional-Ruby/dp/0321944275)
* [Ruby on Rails Guides](http://guides.rubyonrails.org/)
* [The RSpec Book](http://pragprog.com/book/achbd/the-rspec-book)
* [The Cucumber Book](http://pragprog.com/book/hwcuc/the-cucumber-book)
* [Everyday Rails Testing with RSpec](https://leanpub.com/everydayrailsrspec)
* [Better Specs for RSpec](http://betterspecs.org)

# Contributing - Đóng góp

Nothing written in this guide is set in stone. Đó là mong muốn của tác giả được làm việc cùng với những ai yêu thích phong cách lập trình Rails, để chúng ta có thể tạo ra một tài nguyên ultimately
có lợi cho toàn cộng đồng Ruby.

Hãy thoải mái tạo các ticket hoặc gửi các pull request để phát triển thêm. Chân thành cảm ơn đóng góp của bạn!

Bạn cũng có thể hỗ trợ dự án (và RuboCop) với đóng góp tài chính thông qua
[gittip](https://www.gittip.com/bbatsov).

[![Hỗ trợ qua Gittip](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

## Làm sao để đóng góp?

Dễ thôi, làm theo [hướng dẫn đóng góp](https://github.com/bbatsov/rails-style-guide/blob/master/CONTRIBUTING.md).

# Giấy phép

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
Công trình này được cấp phép theo [Creative Commons Attribution 3.0 Unported
License](http://creativecommons.org/licenses/by/3.0/deed.en_US)

# Chia sẻ 

Một hướng dẫn phong cách lập trình hướng cộng đồng sẽ vô ích với cộng đồng nếu người ta không biết nó tồn tại. Xin tweet về nó, chia sẻ nó cho bạn bè mình và đồng nghiệp. Mỗi góp ý, phê bình hay quan điểm của bạn đều giúp chúng ta hoàn thiện hướng dẫn này ngày một tốt hơn. Và chẳng phải chúng ta muốn một hướng dẫn tốt nhất có thể đấy sao?

Thân ái,<br/>
[Bozhidar](https://twitter.com/bbatsov)
