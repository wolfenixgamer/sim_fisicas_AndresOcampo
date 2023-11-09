#include <iostream>
#include <SFML/Graphics.hpp>
#include <Box2DBox2D.h>

constexpr float SCALE = 30.0F;

int main() {
    sf::RenderWindow window(sf::VideoMode(800, 600), "Examen parcial 2");

    b2Vec2 gravity(0.0f, 9.8f);
    b2World world(gravity);

    b2BodyDef ballBodyDef;
    ballBodyDef.type = b2_dynamicBoby;
    ballBodyDef.position.Set(2.0f,0.0f);
    b2Body* ballBody = world.CreateBody(&ballBodyDef);

    b2CircleShape ballShape;
    ballShape.m_radius = 0.5f;

    b2FixtureDef ballFixtureDef;
    ballFixtureDef.shape = &ballShape;
    ballFixtureDef.density = 1.0f;
    ballFixtureDef.friction = 0.3f;

    ballBody->CreateFixture(&ballFixtureDef);

    b2BodyDef rampBodyDef;
    rampBodyDef.type = b2_kinematicBody;
    rampBodyDef.position.Set(2.0f, 5.0f);
    b2Body* rampBody = world.CreateBody(&rampBodyDef);

    b2PolygonShape rampShape;
    rampShape.SetAsBox(5.0f, 0.5f);

    b2FixtureDef rampFxtureDef;
    rampFixtureDef.shape = &rampShape;
    rampFixtureDef.friction = 0.5f;

    rampBody->CreateFixture(&rampFxtureDef);

    const int numDom = 5;
    b2Body* domBodies[numDom];

    for (int i = 0; i < numDom; ++i){
        b2BodyDef domBodyDef;
        domBodyDef.type = b2_dynamicBody;
        domBodyDef.position.set(8.0f + i * 1.5f, 4.0f);
        b2Body* domBody = world.CreateBody(&domBodyDef);

        b2PolygonShape domShape;
        domShape.SetAsBox(0.5f, 2.0f);

        b2FixtureDef domFixtureDef;
        domFixtureDef.shape = &domShape;
        domFixtureDef.density = 1.0f;
        domFixtureDef.friction = 0.3f;

        dominoBodies[i] = domBody;
        domBody->CreateFixture(&domFixtureDef);
    }

    b2RevoluteJointDef revoluteJointDef;
    revoluteJointDef.bodyA = ballBody;
    revoluteJointDef.bodyB = rampBody;
    revoluteJointDef.localAnchorA.Set(0.0f, 0.0f);
    revoluteJointDef.localAnchorB.Set(0.0f, 0.0f);
    revoluteJointDef.collideConnected = false;
    world.CreateJoint(&revoluteJointDef);

    b2BodyDef basketBodyDef;
    basketBodyDef.type = b2_staticBody;
    basketBodyDef.position.Set(40.0f, 10.0f);
    b2Body* basketBody = world.CreateBody(&basketBodyDef);

    b2PolygonShape basketShape;
    basketShape.SetAsBox(2.0f, 0.1f);

    b2FixtureDef basketFixtureDef;
    basketFixtureDef.shape = &basketShape;

    basketBody->CreateFixture(&basketFixtureDef);

    while (window.isOpen()) {
        sf::Event event;
        while (window.pollEvent(event)) {
            if (event.type == sf::Event::Closed) {
                window.close();
            }
        }

        float rampPositionX = rampBody->GetPosition().x;
        if (rampPositionX < 2.0f || rampPositionX > 12.0f) {
            rampBody->SetLinearVelocity(b2Vec2(2.0f, 0.0f));
        } else if (rampPositionX > 10.0f) {
            rampBody->SetLinearVelocity(b2Vec2(-2.0f, 0.0f));
        }

        if (rampPositionX > 10.0f) {
            ballBody->ApplyForceToCenter(b2Vec2(100.0f, 0.0f), true);
        }

        world.Step(1.0f / 60.0f, 8, 3);

        window.clear();

        for (int i = 0; i < numDom; ++i) {
            sf::RectangleShape domShape(sf::Vector2f(1.0f * SCALE, 4.0f * SCALE));
            domShape.setOrigin(0.5f * SCALE, 2.0f * SCALE);
            domShape.setPosition(domBodies[i]->GetPosition().x * SCALE, domBodies[i]->GetPosition().y * SCALE);
            domShape.setRotation(domBodies[i]->GetAngle() * 180 / b2_pi);
            window.draw(domShape);
        }

        sf::CircleShape ballShape(ballShape.m_radius * SCALE);
        ballShape.setOrigin(ballShape.m_radius * SCALE, ballShape.m_radius * SCALE);
        ballShape.setPosition(ballBody->GetPosition().x * SCALE, ballBody->GetPosition().y * SCALE);
        window.draw(ballShape);

        sf::RectangleShape rampShape(sf::Vector2f(10.0f * SCALE, 1.0f * SCALE));
        rampShape.setOrigin(5.0f * SCALE, 0.5f * SCALE);
        rampShape.setPosition(rampBody->GetPosition().x * SCALE, rampBody->GetPosition().y * SCALE);
        rampShape.setRotation(rampBody->GetAngle() * 180 / b2_pi);
        window.draw(rampShape);

        sf::RectangleShape basketShape(sf::Vector2f(4.0f * SCALE, 0.2f * SCALE));
        basketShape.setOrigin(2.0f * SCALE, 0.1f * SCALE);
        basketShape.setPosition(basketBody->GetPosition().x * SCALE, basketBody->GetPosition().y * SCALE);
        basketShape.setRotation(basketBody->GetAngle() * 180 / b2_pi);
        window.draw(basketShape);

        window.display();
    }

    return 0;
}
