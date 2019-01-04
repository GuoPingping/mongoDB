```javaScript

数据模型
    内嵌 引用

    db.contacts.insert([
    { "_id": 1, "name": "Anne", "phone": "+1 555 123 456", "city": "London", "status": "Complete" },
    { "_id": 2, "name": "Ivan", "city": "Vancouver" }
    ])

    add a validator:
    
        db.runCommand( {
            collMod: "contacts",
            validator: { $jsonSchema: {
                bsonType: "object",
                required: [ "phone", "name" ],
                properties: {
                    phone: {
                        bsonType: "string",
                        description: "must be a string and is required"
                    },
                    name: {
                        bsonType: "string",
                        description: "must be a string and is required"
                    }
                }
            } },
            validationLevel: "moderate"
        } )


```