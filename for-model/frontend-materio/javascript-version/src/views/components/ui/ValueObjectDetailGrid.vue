forEach: ValueObject
fileName: {{namePascalCase}}DetailGrid.vue
---
<template>
    <div class="panel">
        <div class="label-title">\{{label}}</div>
        <div class="table-responsive">
            <v-btn v-if="editMode" @click="addDetailRow()">추가</v-btn>
            <v-btn v-if="editMode" style="margin-left: 3px;" @click="detailDeleteRow()">삭제</v-btn>
            <v-table v-if="!editMode">
                <thead>
                    <tr>
                        <th>Id</th>
                        {{#fieldDescriptors}}
                        {{#if (isNotId nameCamelCase)}}
                        {{#if (isPrimitive className)}}
                        {{#if (isStringType (getPrimitiveType className))}}
                        {{else}}
                        <th>{{nameCamelCase}}</th>
                        {{/if}}
                        {{else}}
                        <th>{{namePascalCase}}</th>
                        {{/if}}
                        {{/if}}
                        {{/fieldDescriptors}}
                        <th></th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(detailVal, idx) in selectedRow.{{ toURL nameCamelCase }}" :key="detailVal" >
                        <td class="font-semibold">\{{ idx + 1 }}</td>
                        {{#fieldDescriptors}}
                        {{#if (isNotId nameCamelCase)}}
                        {{#if (isPrimitive className)}}
                        {{#if (isStringType (getPrimitiveType className))}}
                        {{else}}
                        <td class="whitespace-nowrap">{{#getNameCamelCase nameCamelCase}}{{/getNameCamelCase}}</td>
                        {{/if}}
                        {{else}}
                        <td class="whitespace-nowrap">
                            <{{namePascalCase}} v-model="detailVal.{{nameCamelCase}}" :editMode="editMode"/>
                        </td>
                        {{/if}}
                        {{/if}}
                        {{/fieldDescriptors}}
                        <td class="whitespace-nowrap">
                            <Icon icon="mi:delete" @click="deleteRow(detailVal)" />
                        </td>
                    </tr>
                </tbody>
            </v-table>
            <v-table v-else>
                <thead>
                    <tr>
                        <th>Id</th>
                        {{#fieldDescriptors}}
                        {{#if (isNotId nameCamelCase)}}
                        {{#if (isPrimitive className)}}
                        {{#if (isStringType (getPrimitiveType className))}}
                        {{else}}
                        <th>{{nameCamelCase}}</th>
                        {{/if}}
                        {{else}}
                        <th>{{namePascalCase}}</th>
                        {{/if}}
                        {{/if}}
                        {{/fieldDescriptors}}
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(addVal, idx) in newValue" :key="addVal" >
                        <td class="font-semibold">\{{ idx + 1 }}</td>
                        {{#fieldDescriptors}}
                        {{#if (isNotId nameCamelCase)}}
                        {{#if (isPrimitive className)}}
                        {{#if (isStringType (getPrimitiveType className))}}
                        {{else}}
                        <td class="whitespace-nowrap">
                            <{{getPrimitiveType className}} style="margin-left: -5px; width: 150px;" :editMode="editMode" v-model="addVal.{{nameCamelCase}}"/>
                        </td>
                        {{/if}}
                        {{else}}
                        <td class="whitespace-nowrap">
                            <{{namePascalCase}} v-model="addVal.{{nameCamelCase}}" :editMode="editMode"/>
                        </td>
                        {{/if}}
                        {{/if}}
                        {{/fieldDescriptors}}
                    </tr>
                </tbody>
            </v-table>
        </div>
    </div>
</template>
<script>
import BaseDetailGrid from '../base-ui/BaseDetailGrid.vue';
{{#fieldDescriptors}}
{{#if (isNotId nameCamelCase)}}
{{#if (isPrimitiveImport className)}}
{{#if (isStringType (getPrimitiveType className))}}
{{else}}
import {{getPrimitiveType className}} from '../primitives/{{getPrimitiveType className}}.vue'
{{/if}}
{{else}}
{{/if}}
{{/if}}
{{/fieldDescriptors}}

export default {
    name: '{{namePascalCase}}',
    mixins: [BaseDetailGrid],
    components: {
        {{#fieldDescriptors}}
        {{#if (isNotId nameCamelCase)}}
        {{#if (isPrimitiveComponent className)}}
        {{#if (isStringType (getPrimitiveType className))}}
        {{else}}
        {{getPrimitiveType className}},
        {{/if}}
        {{else}}
        {{namePascalCase}},
        {{/if}}
        {{/if}}
        {{/fieldDescriptors}}
    },
    props: {
        label: String,
        editMode: Boolean,
        selectedRow: Object,
    },
    data: ()=>({
    }),
    created(){
    },
    methods: {
    }
}
</script>

<function>
    var importList = []
    var componentList = []

    window.$HandleBars.registerHelper('ifNotNull', function (displayName, name) {
        if(displayName){
	        return displayName;
        }else{
	        return name;
        }
    })
    window.$HandleBars.registerHelper('isNotName', function (className) {
        return (className != 'name')
    })
    window.$HandleBars.registerHelper('isNotId', function (className) {
        return (className != 'id')
    })
    window.$HandleBars.registerHelper('isStringType', function (type) {
        return (type === "String");
    })
    window.$HandleBars.registerHelper('getPrimitiveType', function (className, options) {
        if(className.includes("String")) {
            if(this.isLob) {
                return "LargeObject";
            } else {
                return "String";
            }
        } else if(className.includes("Integer") || className.includes("Long") || className.includes("Double") || className.includes("Float") || className.includes("int")) {
            if(this.isLob) {
                return "LargeObject";
            } else {
                return "Number";
            }
        } else if(className.includes("Boolean")) {
            return "Boolean";
        } else if(className.includes("Date")) {
            return "Date";
        }
    })

    window.$HandleBars.registerHelper('isPrimitive', function (className) {
        if(className.includes("String") || className.includes("Integer") || className.includes("Long") || className.includes("Double") || className.includes("Float")
                || className.includes("Boolean") || className.includes("Date")){
            return true;
        } else {
            return false;
        }
    })
    window.$HandleBars.registerHelper('isPrimitiveImport', function (className) {
        if(!importList.includes(className)){
            importList.push(className)
            if(className.includes("String") || className.includes("Integer") || className.includes("Long") || className.includes("Double") || className.includes("Float")
                || className.includes("Boolean") || className.includes("Date")){
                return true;
            } else {
                return false;
            }
        }else{
            return false;
        }
    })
    window.$HandleBars.registerHelper('isPrimitiveComponent', function (className) {
        if(!componentList.includes(className)){
            componentList.push(className)
            if(className.includes("String") || className.includes("Integer") || className.includes("Long") || className.includes("Double") || className.includes("Float")
                || className.includes("Boolean") || className.includes("Date")){
                return true;
            } else {
                return false;
            }
        }else{
            return false;
        }
    })
    window.$HandleBars.registerHelper('getNameCamelCase', function (nameCamelCase) {
        if(nameCamelCase){
            var tdVal = '{{ detailVal.' + nameCamelCase + ' }}'
            return tdVal
        }
    })

    window.$HandleBars.registerHelper('toURL', function (className) {

        var pluralize = function(value, revert){

        var plural = {
            '(quiz)$'               : "$1zes",
            '^(ox)$'                : "$1en",
            '([m|l])ouse$'          : "$1ice",
            '(matr|vert|ind)ix|ex$' : "$1ices",
            '(x|ch|ss|sh)$'         : "$1es",
            '([^aeiouy]|qu)y$'      : "$1ies",
            '(hive)$'               : "$1s",
            '(?:([^f])fe|([lr])f)$' : "$1$2ves",
            '(shea|lea|loa|thie)f$' : "$1ves",
            'sis$'                  : "ses",
            '([ti])um$'             : "$1a",
            '(tomat|potat|ech|her|vet)o$': "$1oes",
            '(bu)s$'                : "$1ses",
            '(alias)$'              : "$1es",
            '(octop)us$'            : "$1i",
            '(ax|test)is$'          : "$1es",
            '(us)$'                 : "$1es",
            '([^s]+)$'              : "$1s"
        };

        var singular = {
            '(quiz)zes$'             : "$1",
            '(matr)ices$'            : "$1ix",
            '(vert|ind)ices$'        : "$1ex",
            '^(ox)en$'               : "$1",
            '(alias)es$'             : "$1",
            '(octop|vir)i$'          : "$1us",
            '(cris|ax|test)es$'      : "$1is",
            '(shoe)s$'               : "$1",
            '(o)es$'                 : "$1",
            '(bus)es$'               : "$1",
            '([m|l])ice$'            : "$1ouse",
            '(x|ch|ss|sh)es$'        : "$1",
            '(m)ovies$'              : "$1ovie",
            '(s)eries$'              : "$1eries",
            '([^aeiouy]|qu)ies$'     : "$1y",
            '([lr])ves$'             : "$1f",
            '(tive)s$'               : "$1",
            '(hive)s$'               : "$1",
            '(li|wi|kni)ves$'        : "$1fe",
            '(shea|loa|lea|thie)ves$': "$1f",
            '(^analy)ses$'           : "$1sis",
            '((a)naly|(b)a|(d)iagno|(p)arenthe|(p)rogno|(s)ynop|(t)he)ses$': "$1$2sis",        
            '([ti])a$'               : "$1um",
            '(n)ews$'                : "$1ews",
            '(h|bl)ouses$'           : "$1ouse",
            '(corpse)s$'             : "$1",
            '(us)es$'                : "$1",
            's$'                     : ""
        };

        var irregular = {
            'move'   : 'moves',
            'foot'   : 'feet',
            'goose'  : 'geese',
            'sex'    : 'sexes',
            'child'  : 'children',
            'man'    : 'men',
            'tooth'  : 'teeth',
            'person' : 'people',
            'index'  : 'indexes'
        };

        var uncountable = [
            'sheep', 
            'fish',
            'deer',
            'moose',
            'series',
            'species',
            'money',
            'rice',
            'information',
            'equipment'
        ];

        // save some time in the case that singular and plural are the same
        // console.log("value = " + value)
        if(uncountable.indexOf(value.toLowerCase()) >= 0) {
            return this;
        }

        // check for irregular forms
        for(var word in irregular) {

            if(revert) {
                var pattern = new RegExp(irregular[word]+'$', 'i');
                var replace = word;
            } else { 
                var pattern = new RegExp(word+'$', 'i');
                var replace = irregular[word];
            }
            if(pattern.test(value)) {
                return value.replace(pattern, replace);
            }
        }

        if(revert) {
            var array = singular;
        } else {
            var array = plural;
        }

        // check for matches using regular expressions
        for(var reg in array) {

            var pattern = new RegExp(reg, 'i');

            if(pattern.test(value)) {
                return value.replace(pattern, array[reg]);
            }
        }

        return value;
        }

        return pluralize(className.toLowerCase())
    })
</function>