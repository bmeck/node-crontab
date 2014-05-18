[![build status](https://secure.travis-ci.org/dachev/node-crontab.png?branch=master)](http://travis-ci.org/dachev/node-crontab)

# node-crontab
      
Allows reading, manipulating, and writing user crontabs with [node.js](http://nodejs.org).

## Installation
    $ npm install crontab

## Examples
### Simple
```js
require('crontab').load(function(err, crontab) {
  // create with string expression
  var job = crontab.create('ls -la', '0 7 * * 1,2,3,4,5', 'comment 1');

  // create with Date
  var job = crontab.create('ls -lh', new Date(1400373907766));

  // create with comment
  var job = crontab.create('ls -lt', null, 'comment 2');

  // create special: @reboot, @hourly, @daily, @weekly, @monthly, @yearly, @annually, @midnight
  var job = crontab.create('ls -la', '@reboot');

  // remove job object
  var job = crontab.create('ls -lr', '0 7 * * 1,2,3,4,5', 'comment 3');
  crontab.remove(job);

  // remove with conditions
  crontab.remove({command:'ls -lh', comment:/comment 2/});

  // manipulate: every business hour
  var job = crontab.create('ls -l');
  job.minute().at(0);
  job.hour().between(8, 17);
  job.dow().between('mon', 'fri');

  // manipulate: every other hour on weekday nights
  var job = crontab.create('ls -l');
  job.hour().between(19, 0).every(2);
  job.hour().between(0, 6).every(2);
  job.dow().between('mon', 'fri');
  
  // manipulate: summer
  var job = crontab.create('ls -l');
  job.month().between('jun', 'sep');
  
  // manipulate: Christmas
  var job = crontab.create('ls -l');
  job.minute().at(30);
  job.hour().at(9);
  job.dom().on(24);
  job.month().in('dec');

  // show all jobs
  var jobs = crontab.jobs();

  // show jobs with conditions
  var jobs = crontab.jobs({command:'ls -l', comment:/comment 1/});

  // reset jobs to their original state
  crontab.reset();

  // save
  crontab.save(function(err, crontab) {
  
  });

  console.log(crontab);
});
```

### Naive reboot
```js
require('crontab').load(function(err, crontab) {
  if (err) {
    return console.error(err);
  }

  var command = 'ls -l';

  crontab.remove({command:command});
  crontab.create(command, '@reboot');

  crontab.save(function(err, crontab) {

  });
});
```

### More robust reboot and forever
```js
require('crontab').load(function(err, crontab) {
  if (err) {
    return console.error(err);
  }

  var uuid           = '64d967a0-120b-11e0-ac64-0800200c9a66';
  var nodePath       = process.execPath.split('/').slice(0, -1).join('/');
  var exportCommand  = 'export PATH=' + nodePath + ':$PATH';
  var foreverCommand = require('path').join(__dirname, 'node_modules', 'forever', 'bin', 'forever');
  var sysCommand     = exportCommand + ' && ' + foreverCommand + ' start ' + __filename;

  crontab.remove({comment:uuid});
  crontab.create(sysCommand, '@reboot', uuid);

  crontab.save(function(err, crontab) {
    console.log(err)
  });
});
```

## Documentation
API [reference](http://dachev.github.com/node-crontab).

## Compatibility
    
The latest revision of node-crontab is compatible with node --version:

    >= 0.6.0

## License
Copyright 2010, Blagovest Dachev.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

This is a JavaScript port of a Python package with the same name written by
- Martin Owens <doctormo at gmail com>
