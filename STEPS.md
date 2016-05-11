# Step 1: Create services
### Aka. hide low level abstractions behind services

1. Use webpack’s app.bundle.js instead of normal app.js > rewrite it in the index.html
2. Move application libs into app.js using require (import isn’t working :( )
3. Look at phone-list.ctrl.js and phone.ctrl.js, there are two usage, get all phones and get one
4. Create PhoneRepository service and speak about the new structure, especially about the factory method. 
5. Move getAll from phone-list.ctrl.js to the repository service. Remove $http dep. Use ES6 arrow functions.
6. Add PhoneRepository to the app.js
7. Move getOne from phone.ctrl.js to the repository service. Remove $http dep.
8. Look at the CarrierSelect directive, there is a $http here
9. Add CarrierRepository service, add it to the app.js, create the method to get the carriers
10. Use the CarrierRepository in the CarrierSelect directive


# Step 2: The first component

1. Create components directory for the future libraries
2. Create copyright component from the directive. Speak about why it is better.
3. Remove old copyright directive
4. Change the $rootScope injection to an application wide constant and pass it using the DI. Introduce $ctrl in the template.
5. Remove the $rootScope injections in phone.ctrl.js and phone-list.ctrl.js


# Step 3: One-way-binding
### Aka. get rid of ng-include with a simple component using only downward communication

1. Get rid of ng-include, create a new component for list-item as phone-list-item. Use one-way-binding for the phone.
2. Change template to use $ctrl
3. Add new phone-list-item component to app.js
4. Use phone-list-item in the phone-list-ctrl template
5. Refactor shortDescription filter by moving it to a new directory and transform it to the OOP version. Bind the transform in the factory as Angular2 is doing it.
6. Remove the old shortDescription and it’s require call
7. Refactor out some method to other class methods and create a MAX_LENGTH const

# Step 4: Components emitting events
### Aka. create dumb component which is emitting an event to communicate with it’s parent

1. Create new search-box component. Speak about the upward communication instead of the two-way data binding
2. Change the ng-model to ng-keyup in the template. Call a change method with the event.
3. Call the onChange event with the event target value
4. Use the new search-box component instead of the old one, add it to the app.js and get rid of the old one
5. Bind the search-box on-change event to a new setSearchText method in the phone-list.ctrl. The setSearchText method is modifying the $scope’s searchText
6. Move the getFilteredPhones call into the seatSearchText and get rid of the $watch!


# Step 5: Dumb component with proper data flow

1. Investigate carrier select directive. Create a new component for the dumb select called bootstrap-select. Copy the carrier template into it
2. Rename isCarrierSelectorOpened to isOpened and use it from the $ctrl, add it with default false to the constructor
3. Modify the toggler click event to use a toggle method from the component
4. Use $ctrl.select instead of carrierSelect and $ctrl.options 
5. Add one-way input for options and an onSelect event output. Call onSelect on select
6. Show placeholder text if there is no selectedOption with a displaySelectedOption() method. Get placeholder as string input (@)
7. Add new bootstrap-select to the app
8. Use bootstrap-select in carrier selector directive
9. Remove the unnecessary $scope.isCarrierSelectorOpened property from the carrier selector directive


# Step 6: Smart component wrapping a dumb one

1. Create carrier-select component and copy the carrier selector files into it
2. Modify the template to something we want to achieve, so carriers will become $ctrl.carriers, and the selectCarrier could be a simple $ctrl.select
3. Create the initial component code, for the bindings we want a single output to tell the parent that the value is changed (instead of broadcasting a rootScope message). The only dependency will be the carrierRepository because it will be used for the smart part
4. Select method only delegates to onSelect (maybe someone can use it explicitly from the template)
5. Carriers should be loaded on $onInit
6. Add carrier-select component to the app and delete the old require
7. Add on-select event to the carrier-select usage in the phone-list.tpl. Set it using a setSelectedCarrier(carrier) method, remove both of the $watches for this value in the phone-list.ctrl. Remove $rootScope dep also.


# Step 7: Move high-level filtering to separate service
1. Create filtered-phone-list service, it depends on the $filter and the phoneRepository
2. Move all of the filtering functionalities into the filtered phone repository:
   1. Setter methods for setSelectedCarrier and setSearchText
   2. getAll and hasAny method to get the internal phones and it’s size check
   3. Load to load the repository
   4. Keep the phones and allPhones
1. Add it to the app
2. Use this service as “phones” in the phone-list.ctrl instead of the current deps, remove all other deps. Load the phones using the service’s load.
3. Use phones in the template for the setters, for the ng-repeat and the ng-if


# Step 8: Finalize architecture
### Aka. get rid of controllers and create application level components
1. Move phone.ctrl and phone.tpl into a component called phone-app. It will be the phone pages root component
2. It only has the phoneRepository and the $routeParams deps. Save the repository and the $routeParams.phoneId as phoneId in the class instance. Add the initial phone property as well
3. Run the phoneRepository.getOne in the $phoneInit
4. Modify the template to use the $ctrl before the variables
5. Add phone-app component to the app, remove the old require
6. Modify routing to use a simple template with <phone-app>
7. Use the same methods to move the phone-list.ctrl into a component and use as a <phone-list-app> root component
8. Import angular and the router using ES6 import, remove the corresponding script embeds from the html
