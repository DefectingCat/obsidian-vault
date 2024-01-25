
## JSON

```rust
let data = r#"
{
    "name": "xfy",
    "age": 14,
    "test": null
}
"#;
let unamed: Value = serde_json::from_str(data)?;
println!("access unamed {:?}", unamed["name"].as_str());
let named: Test = serde_json::from_str(data)?;
println!("access named {:?}", named.name);

let named = Test::default();
let unamed = json!({
    "name": "xfy",
    "age": 14,
    "test": null
});
println!("unamed json string {}", serde_json::to_string(&unamed)?);
println!("named json string {}", serde_json::to_string(&named)?);
```

```go
	data := `
        {
            "name": "xfy",
            "age": 14,
            "test": null
        }
        `
	var unamed map[string]any
	if err := json.Unmarshal([]byte(data), &unamed); err != nil {
		log.Fatal(err)
	}
	log.Printf("access unamed %v", unamed["name"])
	var named Test
	if err := json.Unmarshal([]byte(data), &named); err != nil {
		log.Fatal(err)
	}
	log.Printf("access named %v", named.Name)

	{
		named := Test{
			Name: "xfy",
			Age:  14,
			Test: nil,
		}
		unamed := map[string]any{
			"name": "xfy",
			"age":  14,
			"test": nil,
		}
		named_str, err := json.Marshal(named)
		if err != nil {
			log.Fatal(err)
		}
		unamed_str, err := json.Marshal(unamed)
		if err != nil {
			log.Fatal(err)
		}
		log.Printf("unamed json string %v", string(unamed_str))
		log.Printf("named json string %v", string(named_str))
	}
```