# SourceryTemplates
This repository contains a collection of templates for [Sourcery](https://github.com/krzysztofzablocki/Sourcery) to help you with common issues you have to face during your Swift development by generating code for you.

## Available Template

- [DecodableDecorated](#DecodableDecorated)

### [DecodableDecorated](Templates/DecodableDecorated.stencil)

- Don't care about CodingKeys
- Don't care about writing the  `init(from decoder: Decoder)` initializer
- Don't care about properties being optional or not
- Don't care about new properties being added or removed without modifing the corresponding `Decodable` code

When you use the `Codable` protocol in Swift and want to use at least one different key from your API (e.g. fill the `text ` property of your class or struct with the content of `description` that comes from a JSON API), you have to completely code your own set of `CodingKeys` and reconstruct the `init(from decoder: Decoder)`. This is all taken care of by this template.

It is decorating, so the code will be placed directly into YOUR code with corresponding markup used by Sourcery. To add a mapping key, simply use the `key` annotation.

e.g.:

**Input**

```swift
class WithAnotherKey: Codable {
    var title: String?
    var id: Int
    // sourcery: key = "description"
    var text: String
}

class NormalCodable: Codable {
    var title: String
    var created: Date
}
```

**Output**

```swift
class WithAnotherKey: Codable {
    var title: String?
    var id: Int
    // sourcery: key = "description"
    var text: String

// sourcery:inline:auto:WithAnotherKey.DecodableDecorated
    enum CodingKey: String, CodingKey {
		case title 
		case id 
		case text  = "description" 
    }

	required init (from decoder: Decoder) throws {
        let values = try decoder.container(keyedBy: CodingKeys.self)

		self.title = try values.decodeIfPresent(String.self, forKey: .title)
		self.id = try values.decode(Int.self, forKey: .id)
		self.text = try values.decode(String.self, forKey: .text)
    }
// sourcery:end
}

class NormalCodable: Codable {
    var title: String
    var created: Date
}
```

Note that the `NormalCodable` didn't get any code as it is not required