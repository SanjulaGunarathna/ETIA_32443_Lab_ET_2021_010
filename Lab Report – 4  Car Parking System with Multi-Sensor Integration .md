#define F_CPU 16000000UL

#include <avr/io.h>
#include <util/delay.h>

#define CAPACITY 10

uint8_t vehicles = 0;
uint8_t previous_detect = 0;

int main(void)
{
    // LEDs
    DDRB |= (1 << DDB0); // D8 Green
    DDRB |= (1 << DDB1); // D9 Yellow
    DDRB |= (1 << DDB2); // D10 Red

    // Reset Button D4
    DDRD &= ~(1 << DDD4);
    PORTD |= (1 << PORTD4);

    // HC-SR04
    DDRD |= (1 << DDD2);     // TRIG
    DDRD &= ~(1 << DDD3);    // ECHO

    while (1)
    {
        uint16_t count = 0;

        // Trigger Pulse
        PORTD &= ~(1 << PORTD2);
        _delay_us(2);

        PORTD |= (1 << PORTD2);
        _delay_us(10);

        PORTD &= ~(1 << PORTD2);

        while (!(PIND & (1 << PIND3)));

        while (PIND & (1 << PIND3))
        {
            count++;
            _delay_us(1);

            if (count > 2000)
                break;
        }

        uint8_t detected = 0;

        if (count < 500)
        {
            detected = 1;
        }

        // State Change Detection
        if (detected && !previous_detect)
        {
            if (vehicles < CAPACITY)
            {
                vehicles++;
            }
        }

        previous_detect = detected;

        // Reset Button
        if (!(PIND & (1 << PIND4)))
        {
            vehicles = 0;
            _delay_ms(200);
        }

        uint8_t available = CAPACITY - vehicles;

        // LED Logic
        PORTB &= ~((1 << PORTB0) | (1 << PORTB1) | (1 << PORTB2));

        if (available > 5)
        {
            PORTB |= (1 << PORTB0);   // Green
        }
        else if (available > 0)
        {
            PORTB |= (1 << PORTB1);   // Yellow
        }
        else
        {
            PORTB |= (1 << PORTB2);   // Red
        }

        _delay_ms(100);
    }
}
