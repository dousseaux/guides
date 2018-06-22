# Ruby Notes

Pedro Dousseau
August 10th, 2017

__Ruby console:__  `$ irb`

___

####1. Strings

__Check if contains substring__

```
my_text.include?(str_to_match)
```

__Replace parts of string__

```
# Replace only the first match
my_text.sub(str_to_match, str_to_replace)
# Replace all the matches
my_text.gsub(str_to_match, str_to_replace)
```

To re-insert the regex match, use \0 in the replacement string. You can use the contents of capturing groups in the replacement string with backreferences \1, \2, \3

__Split by char__

Separate the strings where matched and return an array

```
str.split(str_to_macth)
```

__Remove white spaces from borders__

```
str.strip()
```

__Match strings__

Search for strings and return array with results

```
str.scan(str_to_match)
```

__Regex notes__

If using capturing groups "()" in the expressions, functions won't return what is matched outside of capturing groups.

<br/>

___

####2. Arrays


<br/>

___

####3. Hashes


<br/>

___

####4. JSON

__Generate JSON__

```
JSON.generate(obj)
# or
obj.to_json
```
__Parse JSON to Hash__

```
JSON.parse('{"hello": "goodbye"}')
```


<br/>

___

####5. Conversions


<br/>

___

####6. Conditionals

__IF THEN ELSE END__

```
	if expression
		.... code ....
	elsif expression 
		.... code ....
	else
		.... code ....
	end
		
```

__CASE WHEN__

```
	case size
	when value1
		.... code ....
	else
		.... code ....
	end
```
<br/>

___

####7. Loops

__Simple for loop__

```
n = 4
[*1..n].each do |k|
	print k
end
```

__Loop through array elements__

```
arr.each do |element|
	... Do something with the element ...
end
```

<br/>

___

####8. Time
