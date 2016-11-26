---
layout: post
title: "Tester ses Services avec Angular 2.0.0-beta.15"
date: "2016-04-19 22:35"
categories:
    - javascript
tags:
    - angular2
    - unit tests
comments: true
---

*Attention: Cet article est obsolète depuis la sortie de la version finale d'Angular 2.*

### Déclaration avec `beforeEachProviders` et injection avec `inject`

Afin de pouvoir tester un service, nous avons besoin d'une instance de ce
service.
Bien qu'il soit tout à fait possible de créer cette instance manuellement avec le
mot-clé `new`, une meilleure pratique est d'utiliser l'injection de
dépendances via la méthode `inject`.

Aussi, à la différence des composants où l'on déclarait la liste des services injectables
dans la propriété `providers` du décorateur, dans un test unitaire nous devons
effectuer cette déclaration via la méthode `beforeEachProviders`.

```javascript
import {describe, it, expect, inject, beforeEachProviders} from 'angular2/testing';

describe('MyService', () => {
    beforeEachProviders(() => [MyService]);

    it('should have 2 items', inject([MyService], (myService) => {
        expect(myService.list().length).toBe(2);
    }));

    it('should say hello', inject([MyService], (myService) => {
        expect(myService.hello()).toBe('Hello');
    }));
});
```

Il est possible de factoriser l'instanciation du service avec la méthode
`beforeEach` de Jasmine:

```javascript
import {describe, it, expect, inject, beforeEachProviders, beforeEach} from 'angular2/testing';

describe('MyService', () => {
    let service: MyService;

    beforeEachProviders(() => [MyService]);

    beforeEach(inject([MyService], (raceService) => {
        service = raceService;
    });

    it('should have 2 items', () => {
        expect(service.list().length).toBe(2);
    }));

    it('should say hello', () => {
        expect(myService.hello()).toBe('Hello');
    }));
});
```

### Utiliser `injectAsync` si le test exécute des fonctions asynchrones

La méthode `injectAsync` permet non seulement d'injecter une dépendance mais
aussi d'attendre que tous les appels asynchrones effectués dans le test soient
résolus avant de terminer le test.

```javascript
import {describe, it, expect, injectAsync, beforeEachProviders} from 'angular2/testing';

describe('MyService', () => {
    beforeEachProviders(() => [MyService]);

    it('should have 2 items', injectAsync([MyService], (myService) => {
        return myService.list().then((items) => {
            expect(items.length).toBe(2);
        });
    }));
});
```

### Mocker le comportement d'un service via les espions Jasmine

Imaginons que notre service `MyService` utilise en interne le service
`LocalStorage` pour récupérer une liste d'items.
Dans notre test, nous pouvons très simplement surcharger le comportement du
localStorage afin qu'il retourne un tableau de 2 valeurs prédéfinies.

```javascript
import {describe, it, expect, inject, beforeEachProviders} from 'angular2/testing';

describe('MyService', () => {
    beforeEachProviders(() => [LocalStorageService, MyService]);

    it('should ...', inject([MyService, LocalStorageService], (myService, localStorage) => {
        spyOn(localStorage, 'get').and.returnValue([new Item('...'), new Item('...')]);
        expect(myService.list().length).toBe(2);
        expect(localStorage.get).toHaveBeenCalledWith('items');
    }));
});
```

