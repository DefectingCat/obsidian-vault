```rust
    let file_type = if let Some(ex) = ext.last() {
        debug!("file type {ex}");
        debug!("access path {route}");
        match *ex {
            "png" | "jpg" | "jpeg" => format!("image/{ex}"),
            "svg" => format!("image/svg+xml"),
            "html" | "css" => format!("text/{ex}"),
            "js" => format!("application/javascript"),
            "ico" => format!("image/x-icon"),
            // If equal to path, is access to folder.
            _ if *ex == route => {
                path.push("index.html");
                format!("text/html")
            }
            _ if try_index => {
                path.push("index.html");
                format!("text/html")
            }
            _ => {
                return Err(CandyError::UnknownFileType {
                    file: ex.to_string(),
                })
            }
        }
    } else {
        return Err(CandyError::Unknown);
    };

```