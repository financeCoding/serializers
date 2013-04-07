# Serializers

Serializers is a Dart library for serializing objects into maps.

## INSTALLATION

Add the dependency to your project’s pubspec.yaml.

    name: my_project
    dependencies:
      serializers: any

And `run pub install`.

## HOW TO USE

Suppose, we have the following model.

    class Person {
      String firstName;
      String lastName;

      Person(this.firstName, this.lastName);

      get fullName => "$firstName $lastName";
    }

You can define a serializer for this model as follows:

    class PersonSerializer extends Serializer<Person>{}

Serializing the model is done as follows:

    var person = new Person('Bill', 'Evans');
    var data = new PersonSerializer().serialize(person, object: true);

Where data is equal to:

    {"firstName" : "Bill", "lastName" : "Evans", "fullName" : "Bill Evans"};


### Sync and Async

The `serializeAsync` function returns a future of the serialized data.

    Future<Map> data = new PersonSerializer().serializeAsync(person, object: true);

The `serialize` function returns the data.

    Map data = new PersonSerializer().serialize(person, object: true);


### Object and Map

`object: true` tells serializers to use reflection mirrors. If the given model implements the `[]` operator, you can use map instead.

    Future<Map> data = new PersonSerializer().serializeAsync(person, map: true);
    Map data = new PersonSerializer().serialize(person, map: true);

In this case, reflection mirrors won't be used.


### Configuration

A serializer can be configured using the following properties: fields, root, custom, postProcessing.

    class PersonSerializer extends Serializer<Person>{
      get fields => ["firstName"];

      get custom => {
        "customField" => (Person p) => p.lastName.toUpperCase();
      };

      get root => "person";

      Map postProcessing(Map data, Person person){
        data["reversedFullName"] = "${person.lastName} ${person.firstName}";
        return data;
      }
    }

    var person = new Person('Bill', 'Evans');
    new PersonSerializer().serialize(person, object: true);

Returns:

    {"person" => {"firstName" : "Bill", "customField" : "EVANS", "reversedFullName" : "Evans Bill"}}