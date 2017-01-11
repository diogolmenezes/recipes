# Sinon

- http://sinonjs.org/

## Stubs

### Testando controllers
```javascript
// nao testando response
it('xxx', function (done) {
    var req = { params: { token: '123456' } };
    var res = {
        set: function () { },
        send: function () { },
        json: function (err) { },
        status: function (responseStatus) { return this; }
    };

    var spy = sinon.spy(controller.voiceSender, 'callTwiml');
    controller.getVoiceMessage(req, res, function () { });
    expect(spy.calledOnce).to.be.ok;
    expect(spy.calledWith(req.params.token)).to.be.ok;
    done();
});

// testando response
it('xxxx', function (done) {
    var req = { params: { number: '21444444444', token: '123456' } };
    var res = {
        send: function () { },
        json: function (obj) {
            expect(obj.message).to.be.equal('sucesso');
        },
        status: function (responseStatus) {
            expect(responseStatus).to.be.equal(200);
            return this;
        }
    };

    var stub = sinon.stub(controller.oiTokenService, 'validateAsync', function () {
        return bluebird.resolve('sucesso');
    });

    controller.validate(req, res, null);
    stub.restore();
    done();
});

```


### Retornando valores com callsArgWith para stubs que usam callbacks

```javascript
// erro
var stub = sinon.stub(voiceSender.twilio.calls, 'create').callsArgWith(1, 'um erro qualquer', null);

voiceSender.send(fixtures.number, fixtures.token, function (err, result) {
    expect(stub.calledOnce).to.be.ok;
    expect(err).to.be.equal('um erro qualquer');
    stub.restore();
    done();
});

// sucesso
var stub = sinon.stub(voiceSender.twilio.calls, 'create').callsArgWith(1, null, 'sucesso');
```

### Retornando valores de stubs async

```javascript
// sucesso
var stub = sinon.stub(smsSender.soap, 'sendSMSAsync', function () {
    return bluebird.resolve();
});

smsSender.send(fixtures.number, fixtures.token, function (err, result) {
    expect(stub.calledOnce).to.be.ok;
    expect(result).to.be.equal('sucesso');
    stub.restore();
    done();
});

// cai no catch
var stub = sinon.stub(smsSender.soap, 'sendSMSAsync', function () {
    return bluebird.reject('um erro qualquer');
});

smsSender.send(fixtures.number, fixtures.token, function (err, result) {
    expect(err).to.be.equal('um erro qualquer');
    stub.restore();
    done();
});

// cai no error
var stub = sinon.stub(smsSender.soap, 'sendSMSAsync', function () {
    return bluebird.reject(new bluebird.OperationalError('um erro qualquer'));
});

smsSender.send(fixtures.number, fixtures.token, function (err, result) {
    expect(err.cause).to.be.equal('um erro qualquer');
    stub.restore();
    done();
});
```