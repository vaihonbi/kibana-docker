*   sửa đổi file mongodb\_input.conf với nội dung dưới đây

```plaintext
input {
  mongodb {
    uri => "mongodb://host.docker.internal:27017/ecogreen"
    placeholder_db_dir => "/opt/logstash-mongodb/"
    placeholder_db_name => 
    collection => "ecogreen_logs"  
    batch_size => 5000 
    parse_method => "simple"
  }
}
filter {
  mutate {
    remove_field => ["_id"]
    convert => {
      "input" => "string" 
    }
    convert => {
      "output" => "string" 
    }
  }
}
output {
  stdout {}
  elasticsearch {
    action => "index"
    index => "mongo_log_data"
    hosts => ["elasticsearch:9200"] 
    user => "elastic" 
    password => "1234qwer" 
  }
}   
```

*   mongodb `uri` # thay url (them username & password; vd mongodb://username:password@host.docker.internal:27017/ecogreen)
*   mongodb `collection` thay đổi tên collection của mongodb
*   mongodb `batch_size` số lượng record đã có sẵn trong collection muốn đồng bộ qua elastichsearch
*   elasticsearch `index` index trong elasticsearch (mặc định tạo ra khi chạy import)
*   elasticsearch `user` username elasticsearch (mặc định)
*   elasticsearch `password` password elasticsearch (thay đổi theo .env ELASTICSEARCH_PASSWORD)
*   thay đổi mật khẩu trong .env

**Lưu ý set quyền 777 cho 2 folder volume của docker “elasticsearch” và “logstash-mongo”**