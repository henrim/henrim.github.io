---
layout: post
title: 'Auto unsubscribe Observables in Angular'
tags: 
- Angular
- Observable
- Rxjs
---



Quick post so I can find it again,

[Decorator to automagicly unsubscribe observables](https://netbasal.com/automagically-unsubscribe-in-angular-4487e9853a88)

NB. Needs ngOnDestroy when using AOT
{% raw %}

@Component({
  selector: 'test',
  template: `...`,
})
@AutoUnsubscribe
export class TestComponent {
  one$;
  two$
  three;
  
  constructor( private store: Store<any>, private element : ElementRef) {}
  
  ngOnInit() {
    //...same subscriptions
  }
  
  // Notice that we don't have the ngOnDestroy method anymore
}

@AutoUnsubscribe(["one$", "two$"])
class TestComponent {
  ...
}

{% raw %}
export function AutoUnsubscribe( blackList = [] ) {

  return function ( constructor ) {
    const original = constructor.prototype.ngOnDestroy;

    constructor.prototype.ngOnDestroy = function () {
      for ( let prop in this ) {
        const property = this[ prop ];
        if ( !blackList.includes(prop) ) {
          if ( property && ( typeof property.unsubscribe === "function" ) ) {
            property.unsubscribe();
          }
        }
      }
      original && typeof original === 'function' && original.apply(this, arguments);
    };
  }

}

I had a need to remove some properties from an object when returning it in my Web.Api,
but serializing the full object when storing it to the database.

[JsonIgnore] would remove this for both use cases and was not a solution for this scenario.

The author of NSwag already had a solution in this blogpost: 
[Advanced Newtonsoft.Json: Dynamically rename or ignore properties without changing the serialized class](https://blog.rsuter.com/advanced-newtonsoft-json-dynamically-rename-or-ignore-properties-without-changing-the-serialized-class/#comments)

{% raw %}
```C#

    public class PropertyRenameAndIgnoreSerializerContractResolver : CamelCasePropertyNamesContractResolver
    {
        private readonly Dictionary<Type, HashSet<string>> _ignores;
        private readonly Dictionary<Type, Dictionary<string, string>> _renames;

        public PropertyRenameAndIgnoreSerializerContractResolver()
        {
            _ignores = new Dictionary<Type, HashSet<string>>();
            _renames = new Dictionary<Type, Dictionary<string, string>>();
        }

        public void IgnoreProperty(Type type, params string[] jsonPropertyNames)
        {
            if (!_ignores.ContainsKey(type))
                _ignores[type] = new HashSet<string>();

            foreach (var prop in jsonPropertyNames)
                _ignores[type].Add(prop);
        }

        public void RenameProperty(Type type, string propertyName, string newJsonPropertyName)
        {
            if (!_renames.ContainsKey(type))
                _renames[type] = new Dictionary<string, string>();

            _renames[type][propertyName] = newJsonPropertyName;
        }

        protected override JsonProperty CreateProperty(MemberInfo member, MemberSerialization memberSerialization)
        {
            var property = base.CreateProperty(member, memberSerialization);

            if (IsIgnored(property.DeclaringType, property.PropertyName))
                property.ShouldSerialize = i => false;

            if (IsRenamed(property.DeclaringType, property.PropertyName, out var newJsonPropertyName))
                property.PropertyName = newJsonPropertyName;

            return property;
        }

        private bool IsIgnored(Type type, string jsonPropertyName)
        {
            if (!_ignores.ContainsKey(type))
                return false;

            return _ignores[type].Contains(jsonPropertyName);
        }

        private bool IsRenamed(Type type, string jsonPropertyName, out string newJsonPropertyName)
        {
            Dictionary<string, string> renames;

            if (!_renames.TryGetValue(type, out renames) || !renames.TryGetValue(jsonPropertyName, out newJsonPropertyName))
            {
                newJsonPropertyName = null;
                return false;
            }

            return true;
        }
    }
{% endraw %}



It was then used in a controller action like this:

{% raw %}
```C#
    public class CloudController : ApiController
    {

        CloudController(){
         //   ctor...
        }

        public async Task<IHttpActionResult> Get(Guid cloudid)
        {

            var cloud = await _clouds.GetAsync(cloudid);
            var resolver = new PropertyRenameAndIgnoreSerializerContractResolver();            
            
            resolver.IgnoreProperty(typeof(Connection), "hiddenSettings");
            
            var settings = new JsonSerializerSettings
            {
                ContractResolver = resolver,
                Formatting = Formatting.Indented
            };

            settings.Converters.Add(new StringEnumConverter());
            return Json(cloud, settings);
        }
    }

